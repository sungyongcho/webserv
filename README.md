# webserv

> HTTP/1.1 server written in C++98 with a kqueue event loop, CGI support, and Nginx-like configuration.

## Overview

An HTTP/1.1 web server in C++98 with no external dependencies. It handles concurrent connections with non-blocking sockets on a single kqueue event loop, parses an Nginx-inspired configuration file, serves GET, HEAD, POST, PUT and DELETE requests, and runs CGI scripts (for example PHP) through fork/execve. It serves static sites and handles file uploads.

A 42 Seoul team project (Jul - Aug 2021). My parts: the kqueue event loop, the non-blocking listening and connection socket layer, the configuration-file tokenizer, and CGI execution through fork/execve with CGI environment variables (tested with 42's cgi_tester). Teammates wrote most of the configuration directive parsing, the chunked request decoding and the rest of the request and response handling.

## Tech Stack

| Layer | Technologies |
|-------|-------------|
| Language | C++98 |
| I/O | kqueue (non-blocking, single-threaded event loop; macOS/BSD) |
| Protocol | HTTP/1.1 (the subset the 42 subject requires) |
| Build | Makefile |

## Key Features

- Non-blocking sockets on a single kqueue event loop that handles all connections
- Nginx-like configuration file format with server blocks, location directives, and route rules
- GET, POST, PUT, DELETE, and HEAD method support
- CGI execution through fork/execve with CGI environment variables
- Directory listing (autoindex), default index files, and custom error pages
- HTTP redirections (301, 302) and return directives
- Client body size limits and chunked transfer encoding
- File upload handling with configurable upload directories
- Multiple virtual servers on different ports with server_name matching

## Architecture

```
webserv/
├── src/webserv/
│   ├── config/
│   │   ├── Tokenizer.hpp/cpp        # Config file tokenizer
│   │   ├── HttpConfig.hpp/cpp       # http block parsing
│   │   ├── ServerConfig.hpp/cpp     # server block configuration
│   │   └── LocationConfig.hpp/cpp   # location directive configuration
│   ├── socket/
│   │   ├── Kqueue.hpp/cpp           # kqueue event registration and polling
│   │   ├── Cycle.hpp/cpp            # Event loop
│   │   ├── Listening.hpp/cpp        # Listening sockets
│   │   ├── Connection.hpp/cpp       # Connection state
│   │   └── SocketManager.hpp/cpp    # Socket lifecycle
│   ├── message/
│   │   ├── Request.hpp/cpp          # HTTP request parsing (method, URI, headers)
│   │   ├── Response.hpp/cpp         # HTTP response construction
│   │   └── handler/
│   │       ├── RequestHandler.hpp/cpp   # Request validation and routing
│   │       ├── ResponseHandler.hpp/cpp  # Response generation (methods, autoindex)
│   │       └── CgiHandler.hpp/cpp       # CGI process execution
│   ├── logger/                      # Logging helpers
│   └── Exceptions.hpp               # Custom exception hierarchy
├── config/
│   └── sample.conf                  # Example Nginx-style configuration
├── var/www/html/                    # Default document root
└── Makefile
```

## Getting Started

### Prerequisites

```bash
# C++98 compatible compiler (clang++)
# macOS (kqueue); it does not build on Linux
```

### Installation

```bash
git clone https://github.com/sungyongcho/webserv.git
cd webserv
make
```

### Usage

```bash
# Start with default config
./webserv

# Start with custom config
./webserv config/sample.conf

# Test with curl
curl http://localhost:8080/
curl -X POST -d "data" http://localhost:8080/upload
curl -X DELETE http://localhost:8080/delete_test/file.txt
```

### Tester (Brief)

The `tester/` directory includes official 42 tester binaries for quick validation.

```bash
# Terminal A: run the server
./webserv config/sample.conf

# Terminal B: run testers
./tester/tester
./tester/cgi_tester
```

## What This Demonstrates

- **Systems Programming**: An HTTP/1.1 server in C++98 with non-blocking I/O, an event-driven architecture and no external dependencies.
- **Protocol Implementation**: HTTP/1.1 request parsing and response generation, including chunked request bodies and CGI.
- **Concurrent I/O**: One kqueue event loop handles all socket reads and writes on a single thread.

## License

This project was built as a team project at 42 school.

---

*Part of [sungyongcho](https://github.com/sungyongcho)'s project portfolio.*
