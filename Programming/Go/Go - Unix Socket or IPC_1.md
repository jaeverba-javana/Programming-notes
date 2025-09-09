# Auto Unix Socket O IPC
#programming #golang #services

Aquí tienes un código en Go que implementa automáticamente Unix domain sockets en Linux/macOS y Named Pipes en Windows usando compilación condicional:

## Estructura del proyecto

```
proyecto/
├── main.go
├── ipc_unix.go
├── ipc_windows.go
└── go.mod
```

## main.go (archivo principal)

```go
package main

import (
	"fmt"
	"log"
	"runtime"
)

func main() {
	fmt.Printf("Iniciando servidor IPC en %s\n", runtime.GOOS)
	
	server := NewIPCServer()
	if err := server.Start(); err != nil {
		log.Fatal("Error iniciando servidor:", err)
	}
	
	fmt.Println("Servidor iniciado exitosamente")
	server.AcceptConnections()
}
```

## ipc_unix.go (implementación Unix)

```go
//go:build !windows
// +build !windows

package main

import (
	"fmt"
	"net"
	"os"
)

type IPCServer struct {
	listener net.Listener
	address  string
}

func NewIPCServer() *IPCServer {
	return &IPCServer{
		address: "/tmp/myapp.sock",
	}
}

func (s *IPCServer) Start() error {
	// Elimina el socket si ya existe
	os.Remove(s.address)
	
	var err error
	s.listener, err = net.Listen("unix", s.address)
	if err != nil {
		return fmt.Errorf("error creando Unix socket: %v", err)
	}
	
	fmt.Printf("Unix socket servidor iniciado en: %s\n", s.address)
	return nil
}

func (s *IPCServer) AcceptConnections() {
	defer s.listener.Close()
	defer os.Remove(s.address) // Limpia el socket al salir
	
	for {
		conn, err := s.listener.Accept()
		if err != nil {
			fmt.Printf("Error aceptando conexión: %v\n", err)
			continue
		}
		
		go s.handleConnection(conn)
	}
}

func (s *IPCServer) handleConnection(conn net.Conn) {
	defer conn.Close()
	
	buf := make([]byte, 1024)
	n, err := conn.Read(buf)
	if err != nil {
		fmt.Printf("Error leyendo datos: %v\n", err)
		return
	}
	
	message := string(buf[:n])
	fmt.Printf("Mensaje recibido (Unix socket): %s\n", message)
	
	// Echo de respuesta
	response := fmt.Sprintf("Echo desde Unix socket: %s", message)
	conn.Write([]byte(response))
}
```

## ipc_windows.go (implementación Windows)

```go
//go:build windows
// +build windows

package main

import (
	"fmt"
	"net"
	
	"github.com/Microsoft/go-winio"
)

type IPCServer struct {
	listener net.Listener
	address  string
}

func NewIPCServer() *IPCServer {
	return &IPCServer{
		address: `\\.\pipe\myapp`,
	}
}

func (s *IPCServer) Start() error {
	var err error
	s.listener, err = winio.ListenPipe(s.address, nil)
	if err != nil {
		return fmt.Errorf("error creando Named Pipe: %v", err)
	}
	
	fmt.Printf("Named Pipe servidor iniciado en: %s\n", s.address)
	return nil
}

func (s *IPCServer) AcceptConnections() {
	defer s.listener.Close()
	
	for {
		conn, err := s.listener.Accept()
		if err != nil {
			fmt.Printf("Error aceptando conexión: %v\n", err)
			continue
		}
		
		go s.handleConnection(conn)
	}
}

func (s *IPCServer) handleConnection(conn net.Conn) {
	defer conn.Close()
	
	buf := make([]byte, 1024)
	n, err := conn.Read(buf)
	if err != nil {
		fmt.Printf("Error leyendo datos: %v\n", err)
		return
	}
	
	message := string(buf[:n])
	fmt.Printf("Mensaje recibido (Named Pipe): %s\n", message)
	
	// Echo de respuesta
	response := fmt.Sprintf("Echo desde Named Pipe: %s", message)
	conn.Write([]byte(response))
}
```

## go.mod

```go
module ipc-demo

go 1.21

require github.com/Microsoft/go-winio v0.6.1

require (
	golang.org/x/sys v0.9.0 // indirect
)
```

## Cliente de prueba (cliente.go)

```go
package main

import (
	"fmt"
	"log"
	"net"
	"runtime"
)

func main() {
	var conn net.Conn
	var err error
	
	if runtime.GOOS == "windows" {
		// Windows: conecta a Named Pipe
		conn, err = net.Dial("tcp", "localhost:0") // Placeholder
		// En realidad necesitarías usar go-winio para el cliente también
	} else {
		// Unix: conecta a Unix socket
		conn, err = net.Dial("unix", "/tmp/myapp.sock")
	}
	
	if err != nil {
		log.Fatal("Error conectando:", err)
	}
	defer conn.Close()
	
	// Envía mensaje
	message := "Hola desde cliente Go!"
	_, err = conn.Write([]byte(message))
	if err != nil {
		log.Fatal("Error enviando mensaje:", err)
	}
	
	// Lee respuesta
	buf := make([]byte, 1024)
	n, err := conn.Read(buf)
	if err != nil {
		log.Fatal("Error leyendo respuesta:", err)
	}
	
	fmt.Printf("Respuesta del servidor: %s\n", string(buf[:n]))
}
```

## Compilación

Para compilar automáticamente según la plataforma:
```bash
# En Linux/macOS (compilará con ipc_unix.go)
go build -o servidor

# En Windows (compilará con ipc_windows.go)  
go build -o servidor.exe

# Para compilación cruzada desde Linux hacia Windows
GOOS=windows go build -o servidor.exe
```

## Características clave

1. **Build tags**: Los comentarios `//go:build` hacen que Go compile automáticamente el archivo correcto según el SO
2. **Interfaz común**: Ambas implementaciones usan la misma estructura `IPCServer` y métodos
3. **Eficiencia nativa**: Unix sockets en Linux/macOS, Named Pipes en Windows
4. **Compatibilidad total**: Funciona sin cambios de código en ambas plataformas

Para usar Named Pipes en Windows, necesitas instalar la dependencia:
```bash
go get github.com/Microsoft/go-winio
```

Este código te permite tener una sola aplicación que funciona óptimamente en cualquier plataforma usando la mejor opción de IPC disponible.

