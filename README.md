# webserv

> HTTP/1.1 server written in C++98 with multiplexing, CGI support, and Nginx-like configuration.

## Overview

A fully functional HTTP/1.1 web server built from scratch in C++98. The server handles concurrent connections using non-blocking I/O with poll/kqueue multiplexing, parses an Nginx-inspired configuration file, supports GET/POST/PUT/DELETE methods, and executes CGI scripts (PHP, Python). Designed to serve static websites, handle file uploads, and proxy requests — all while never blocking or crashing.

This project was developed by a 2-member team at 42 School and received a score of 100/100. I led the core server architecture and implementation.

## Tech Stack

| Layer | Technologies |
|-------|-------------|
| Language | C++98 |
| I/O | poll / kqueue (non-blocking, single-thread multiplexing) |
| Protocol | HTTP/1.1 (RFC 7230-7235 compliant) |
| Build | Makefile |

## Key Features

- Non-blocking I/O multiplexing with poll/kqueue — single event loop handles all connections
- Nginx-like configuration file format with server blocks, location directives, and route rules
- GET, POST, PUT, DELETE, and HEAD method support
- CGI execution for dynamic content (PHP, Python) with environment setup and process management
- Directory listing (autoindex), default index files, and custom error pages
- HTTP redirections (301, 302) and return directives
- Client body size limits and chunked transfer encoding
- File upload handling with configurable upload directories
- Multiple virtual servers on different ports with server_name matching
- Stress-test resilient — the server must never die

## Architecture

```
webserv/
├── src/webserv/
│   ├── config/
│   │   ├── HttpConfig.hpp/cpp       # Config file parsing and tokenization
│   │   ├── ServerConfig.hpp/cpp     # Server block configuration
│   │   └── LocationConfig.hpp/cpp   # Location directive configuration
│   ├── socket/
│   │   └── Connection.hpp/cpp       # Socket management and connection state
│   ├── message/
│   │   ├── Request.hpp/cpp          # HTTP request parsing (method, URI, headers)
│   │   ├── Response.hpp/cpp         # HTTP response construction
│   │   └── handler/
│   │       ├── RequestHandler.hpp/cpp   # Request validation and routing
│   │       └── ResponseHandler.hpp/cpp  # Response generation (methods, autoindex)
│   ├── cgi/
│   │   └── CgiHandler.hpp/cpp       # CGI process execution and I/O
│   └── Exceptions.hpp               # Custom exception hierarchy
├── config/
│   └── sample.conf                  # Example Nginx-style configuration
├── var/www/html/                    # Default document root
└── Makefile
```

## Getting Started

### Prerequisites

```bash
# C++98 compatible compiler (g++, clang++)
# macOS or Linux
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
# macOS
./tester/tester
./tester/cgi_tester

# Linux (make binaries executable once)
chmod +x ./tester/ubuntu_tester ./tester/ubuntu_cgi_tester
./tester/ubuntu_tester
./tester/ubuntu_cgi_tester
```

## What This Demonstrates

- **Systems Programming**: Built a production-style HTTP server from scratch in C++98 with non-blocking I/O, event-driven architecture, and zero external dependencies.
- **Protocol Implementation**: Parsed and generated HTTP/1.1 messages compliant with RFC 7230-7235, handling edge cases like chunked encoding, multipart uploads, and CGI process management.
- **Concurrent I/O**: Implemented single-threaded multiplexing with poll/kqueue where a single event loop handles all socket reads, writes, and CGI pipe communication without blocking.

## License

This project was built as a team project at 42 school.

---

*Part of [sungyongcho](https://github.com/sungyongcho)'s project portfolio.*
