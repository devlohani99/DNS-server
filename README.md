# DNS Server Implementation in Rust

A comprehensive DNS server built from scratch using Rust, featuring both proxy and recursive resolution capabilities. This project demonstrates the complete implementation of DNS protocol handling, from basic packet parsing to full recursive lookups.

## Features

- Complete DNS Protocol Implementation: Full support for DNS packet structure including headers, questions, answers, authority, and additional sections
- Multiple Record Types: Support for A, NS, CNAME, SOA, MX, TXT, and AAAA records
- Proxy Server Mode: Forward queries to upstream DNS servers
- Recursive Resolution: Perform complete recursive DNS lookups starting from root servers
- Efficient Packet Compression: Handle DNS name compression and jump directives
- UDP Transport: Standard DNS over UDP with 512-byte packet size compliance

## Project Structure

```
src/
├── protocol/
│   ├── byte_packet_buffer.rs    # Packet parsing and compression handling
│   ├── dnsheader.rs            # DNS header implementation
│   ├── dnsquestion.rs          # DNS question section
│   ├── dnsrecord.rs            # DNS resource records
│   ├── dnspacket.rs            # Complete DNS packet structure
│   ├── querytype.rs            # DNS query type definitions
│   └── resultcode.rs           # DNS response codes
└── main.rs                     # Server implementation and main logic
```

## Technical Implementation

### Protocol Layer

The DNS protocol implementation handles the standard DNS packet format:

- Header Section: Contains query/response metadata, flags, and counters
- Question Section: Specifies the domain name and record type being queried  
- Answer Section: Contains the requested DNS records
- Authority Section: Lists authoritative name servers
- Additional Section: Provides supplementary information

### Supported DNS Record Types

| Type  | ID | Description | Implementation Status |
|-------|----|-----------|--------------------|
| A     | 1  | IPv4 Address | Complete |
| NS    | 2  | Name Server | Complete |
| CNAME | 5  | Canonical Name | Complete |
| SOA   | 6  | Start of Authority | Complete |
| MX    | 15 | Mail Exchange | Complete |
| TXT   | 16 | Text Record | Complete |
| AAAA  | 28 | IPv6 Address | Complete |

### Packet Compression

The implementation includes sophisticated handling of DNS name compression:
- Label-based domain name encoding
- Jump directive processing for space optimization
- Cycle detection to prevent infinite loops
- Efficient memory usage within 512-byte packet limits

## Getting Started

### Prerequisites

- Rust (latest stable version)
- Cargo package manager

### Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd DNS-Server-Rust
```

2. Build the project:
```bash
cargo build --release
```

3. Run the DNS server:
```bash
cargo run
```

The server will start on port 2053 by default.

### Testing the Server

Use `dig` to test DNS queries:

```bash
# Query A record
dig @127.0.0.1 -p 2053 google.com

# Query MX record
dig @127.0.0.1 -p 2053 yahoo.com MX

# Query SOA record  
dig @127.0.0.1 -p 2053 example.com SOA
```

## Example Output

### A Record Query
```
;; ANSWER SECTION:
google.com.             74      IN      A       142.250.183.238
```

### MX Record Query
```
;; ANSWER SECTION:
yahoo.com.              1673    IN      MX      1 mta5.am0.yahoodns.net.
yahoo.com.              1673    IN      MX      1 mta6.am0.yahoodns.net.
yahoo.com.              1673    IN      MX      1 mta7.am0.yahoodns.net.
```

## Server Modes

### Proxy Mode
Forwards DNS queries to upstream servers (default: 8.8.8.8):
- Fast response times using established resolvers
- Transparent query forwarding
- Suitable for local network DNS serving

### Recursive Mode
Performs complete recursive resolution from root servers:
- Independent DNS resolution capability
- Educational insight into DNS hierarchy
- Full control over resolution process

## Architecture Details

### Core Components

1. **BytePacketBuffer**: Handles binary DNS packet parsing with support for:
   - Variable-length encoding
   - Name compression and decompression
   - Safe memory access with bounds checking

2. **DNS Records**: Comprehensive record type support with:
   - Type-safe enum representations
   - Efficient serialization/deserialization
   - Extensible design for additional record types

3. **Recursive Resolver**: Implements the complete DNS resolution algorithm:
   - Root server bootstrapping
   - Iterative query processing
   - Authority server traversal

### Performance Considerations

- Zero-copy packet parsing where possible
- Efficient string handling for domain names
- Minimal memory allocations during packet processing
- UDP socket reuse for multiple queries

## Development and Testing

### Running Tests
```bash
cargo test
```

### Debug Mode
```bash
cargo run -- --debug
```

### Packet Analysis
The server provides detailed packet analysis output for debugging:
- Header field inspection
- Question section parsing
- Answer record details
- Authority and additional section contents

## Contributing

Contributions are welcome! Areas for improvement include:

- **Caching Layer**: Implement DNS response caching for improved performance
- **IPv6 Support**: Enhanced IPv6 handling and dual-stack operation
- **DNSSEC**: Security extensions support
- **Performance Optimization**: Further optimization of packet processing
- **Configuration**: Runtime configuration for server parameters

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for detailed contribution guidelines.

## Learning Resources

This implementation serves as an educational resource for understanding:
- DNS protocol internals and packet structure
- Network programming in Rust
- Binary protocol parsing techniques
- Recursive algorithm implementation
- System-level network service development

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## References

- [RFC 1035](https://tools.ietf.org/html/rfc1035) - Domain Names - Implementation and Specification
- [Root Name Servers](https://www.internic.net/domain/named.root) - Internet Root Name Server System
- [DNS Record Types](https://en.wikipedia.org/wiki/List_of_DNS_record_types) - Comprehensive list of DNS record types

---

**Note**: This DNS server is designed for educational purposes and local development. For production use, consider additional features like rate limiting, logging, monitoring, and security hardening.
