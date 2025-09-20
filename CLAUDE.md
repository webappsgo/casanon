```
PROJECT SPECIFICATION: CASANON - COMPREHENSIVE ANONYMOUS INFRASTRUCTURE SERVER

PROJECT OVERVIEW:
Casanon is a fully self-hosted, user-friendly, high-security, public-by-default anonymity infrastructure server designed exclusively for Linux systems. It functions as an all-in-one router, VPN server, DNS server (both authoritative and recursive resolver), anonymity proxy aggregator, hidden service host (Tor), eepsite host (I2P), Freenet content host, service manager, and zero-configuration gateway for anonymous protocols. The project provides both users and administrators with a modern, intuitive web UI and comprehensive CLI for configuration, control, statistics monitoring, and secure routing across Tor, I2P, Freenet, DNS, and VPN layers.

CORE ARCHITECTURAL PRINCIPLES:
- Casanon does NOT rely on external configuration files whatsoever
- All settings, configurations, user data, service parameters, and system state are stored in a single unified database (SQLite by default, with PostgreSQL and MySQL support)
- All service configuration files (torrc, i2pd.conf, DNS zone files, WireGuard configs, etc.) are generated dynamically from database data at runtime
- The database is the single source of truth for all system state
- No manual editing of configuration files is ever required or supported
- All changes must go through the web UI, CLI, or API

PROGRAMMING LANGUAGES AND DEPENDENCIES:
- Backend: Go 1.21+ (all core logic, CLI, web server, service management)
- Frontend: Pure HTML5, CSS3, JavaScript (no frameworks, vanilla JS only)
- Database: SQLite (default), PostgreSQL, MySQL/MariaDB support via connection strings
- External service dependencies: Tor, I2Pd, Freenet (Java-based), WireGuard, BIND9 or similar DNS server, fcgiwrap for CGI
- CGI Support: PHP, Ruby, Python, Perl for user websites
- No external Go dependencies except: gorilla/mux, gorilla/sessions, lib/pq, go-sql-driver/mysql, mattn/go-sqlite3, golang-migrate/migrate, golang.org/x/crypto

SUPPORTED ARCHITECTURES AND OPERATING SYSTEMS:
- Architecture: amd64 (x86_64) and arm64 (aarch64) only
- Operating Systems: All major Linux distributions with complete distro-agnostic support
- Specifically tested and supported: Debian 11+, Ubuntu 20.04+, Fedora 35+, Arch Linux, Alpine Linux 3.15+
- Package managers: apt (Debian/Ubuntu), dnf/yum (Fedora/RHEL), pacman (Arch), apk (Alpine)
- Systemd required for service management
- No Docker, containers, or virtualization dependencies

CORE FEATURES SPECIFICATION:

1. ANONYMOUS NETWORKING CORE:
TOR SERVICE INTEGRATION:
- Full Tor support with bridge, relay, and hidden service capabilities
- Tor service starts enabled by default on system installation
- Binds on all network interfaces (IPv4 and IPv6) by default
- Provides both SOCKS5 proxy (default port 9050) and HTTP proxy (default port 8118) endpoints
- Supports Tor hidden services for user websites with automatic .onion address generation
- Control port (9051) enabled for service management
- Tor configuration (torrc) generated dynamically from database settings
- Support for custom Tor bridges and relay configuration through web UI
- Automatic detection of WAN interface for exit relay configuration
- Smart NAT detection using external services (ifconfig.co, ifcfg.us)
- Blocks routing to private networks (RFC 1918, RFC 4193) to prevent abuse, DDoS, spam, and reflection attacks

I2P SERVICE INTEGRATION:
- Full I2P support using i2pd (C++ implementation) for performance
- I2P service starts enabled by default and operates as public node
- Provides SOCKS5 proxy (default port 4447) and HTTP proxy (default port 4444)
- Full support for I2P tunnels configurable through web UI
- Automatic I2P eepsite hosting for user websites with .i2p address generation
- I2P router console integration for advanced users
- Supports both client and server tunnels
- Automatic bandwidth sharing for I2P network health
- Configuration generated dynamically from database

FREENET SERVICE INTEGRATION:
- Full Freenet node hosting with Java-based Freenet implementation
- Freenet service starts enabled by default in public mode
- Default port 8888 for Freenet web interface
- Automatic content sharing enabled for network participation
- Supports both Freenet 0.7 legacy and Hyphanet protocols
- User content publishing through integrated web interface
- Freenet key generation and management for user sites
- Configuration generated dynamically from database

NETWORK SECURITY AND ROUTING:
- All three services (Tor, I2P, Freenet) have outgoing Internet access enabled by default
- Smart WAN interface detection that ignores docker0, lo, virtual interfaces
- Uses external IP detection services (ifconfig.co, ifcfg.us) for WAN verification
- Enforces deny rules for routing to private networks internally (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16, fc00::/7)
- Prevents use as open proxy for abuse, DDoS attacks, spam relay, or reflection attacks
- IPv6 fully supported across all services
- Automatic firewall rule generation for service ports

2. DNS SERVER (AUTHORITATIVE AND RECURSIVE):
AUTHORITATIVE DNS SERVER:
- Full RFC-compliant authoritative DNS server implementation
- Supports complete DNS zone creation, editing, and management through web UI
- Secondary zone transfer support (both inbound and outbound AXFR/IXFR)
- Primary/secondary/reverse zone types supported
- DNSSEC support with automatic key generation and management
- Uses BIND-native zone file format generated from database
- Dynamic zone updates supported
- Per-user zone management with ownership controls
- Administrative approval required for new zone creation
- Zone verification system to prevent unauthorized zone takeovers

RECURSIVE DNS RESOLVER:
- Full recursive DNS resolver with smart caching
- Pihole-style DNS blocklist support with hierarchical categories and subcategories
- Blocklists can be enabled/disabled per category through web UI
- Custom blocklist import and management
- DNS query logging with privacy controls
- Statistics tracking (queries per second, blocked queries, top domains)
- Fallback to public DNS resolvers (1.1.1.1, 8.8.8.8) for unresolved queries
- DNS-over-HTTPS (DoH) and DNS-over-TLS (DoT) endpoints on same port as web UI
- Reverse proxy integration for DoH/DoT certificate management

DNS MANAGEMENT FEATURES:
- Web UI for both admin and per-user zone management
- Support for all standard DNS record types: A, AAAA, CNAME, MX, TXT, NS, SOA, PTR, SRV
- Automatic SOA record management with serial number incrementing
- Zone import/export in BIND format
- DNS zone templates for common configurations
- Real-time DNS propagation testing
- Integration with Let's Encrypt for DNS-01 challenge automation

3. BUILT-IN PROXY ROUTER:
UNIFIED PROXY SYSTEM:
- Unified internal proxy router for seamless Tor/I2P/Freenet access
- Supports both SOCKS4/5 and HTTP(S) proxy protocols
- Smart routing based on destination domain (.onion -> Tor, .i2p -> I2P, .freenet -> Freenet)
- PAC (Proxy Auto-Config) file generation for automatic browser configuration
- Automatic browser and operating system detection for optimal configuration
- Reverse proxy awareness with automatic nginx integration via virtual host configuration
- Per-service proxy endpoints with statistics and monitoring

PROXY CONFIGURATION AND MANAGEMENT:
- Dynamic proxy configuration through web UI
- Per-service toggle controls (enable/disable Tor, I2P, Freenet routing)
- Bandwidth monitoring and limiting per service
- Connection statistics and real-time monitoring
- Proxy authentication support (optional)
- Custom routing rules and domain-based routing
- Integration with web server for seamless user experience

4. BUILT-IN WEB SERVER:
CGI-CAPABLE WEB SERVER:
- Full-featured HTTP/HTTPS web server with CGI support
- CGI execution via fcgiwrap or native internal execution engine
- Built-in support for PHP, Ruby, Python, Perl interpreters
- Static file serving for HTML, CSS, JavaScript, images
- Automatic MIME type detection and serving
- HTTP/2 support for improved performance

USER WEBSITE HOSTING:
- Site directory structure: /var/www/casanon/{username}/{sitename}/
- Automatic directory creation: cgi-bin/, css/, js/, images/, uploads/
- Per-user website hosting with complete isolation
- Support for multiple sites per user
- Automatic index page generation showing service bindings and welcome information

MULTI-PROTOCOL SITE HOSTING:
- Each user can host websites on multiple protocols simultaneously:
  * Clear web (standard HTTP/HTTPS with domain names)
  * Tor hidden services (automatic .onion address generation)
  * I2P eepsites (automatic .i2p address generation)  
  * Freenet sites (automatic Freenet key generation)
- Automatic service binding and configuration
- Cross-protocol content synchronization options

SECURITY AND LOGGING:
- Sandboxed CGI execution with restricted environment variables
- Per-user access logs (access.log) and error logs (error.log)
- Log rotation: monthly rotation with automatic cleanup
- No historical logs retained beyond current month for privacy
- Disk quota enforcement per user (configurable)
- File upload restrictions and virus scanning integration

5. VPN SERVER (WIREGUARD):
WIREGUARD VPN IMPLEMENTATION:
- Modern WireGuard-based VPN server with high performance
- Automatic WireGuard configuration generation from database
- Public DNS fallback through Casanon (1.1.1.1/8.8.8.8 as secondary)
- All DNS queries from VPN clients routed through Casanon DNS server
- Full IPv6 support with automatic address assignment
- NAT traversal and automatic endpoint detection

VPN CLIENT MANAGEMENT:
- Web UI for VPN configuration generation and management
- Automatic QR code generation for mobile device setup
- Per-device configuration files with unique keys
- Device revocation and key regeneration capabilities
- Multi-device support per user (unlimited devices)
- Client-specific statistics and monitoring

VPN SECURITY AND PRIVACY:
- Strong cryptographic defaults (Curve25519, ChaCha20Poly1305)
- Automatic key generation and rotation
- No client-to-client communication by default
- Admin UI shows aggregate bandwidth statistics only
- Individual user/device statistics not exposed on public dashboard
- No QR codes or user counts displayed on public status page
- Perfect forward secrecy with regular key rotation

DNS AND ROUTING:
- All VPN client DNS queries routed through Casanon
- DoH/DoT optionally exposed for VPN clients
- Split tunneling support for advanced users
- Custom routing rules per client
- Bandwidth monitoring and limiting

6. USER SYSTEM AND AUTHENTICATION:
USER REGISTRATION AND VERIFICATION:
- Open self-registration system with email verification required
- Email verification mandatory before account activation
- Password strength requirements and secure password hashing (bcrypt)
- Account recovery via email with secure token system
- User profiles with customizable settings
- If smtp server is not working or smtp is disabled then the first user to register is still primary admin but open registration is disabled until smtp server ic configured and validated as working

SMTP INTEGRATION:
- Full SMTP support for email notifications and verification
- Configurable SMTP settings: host, port, authentication
- TLS support with three modes: None, SSL (implicit), STARTTLS (explicit)
- Customizable From name and email address (default: no-reply@hostname)
- SMTP connection testing with actual email delivery verification
- Email template system for verification, password reset, notifications

TOKEN-BASED API ACCESS:
- Three-tier token system with granular permissions:
  * admin: Full system control including user management, service control, configuration
  * global: All user features except administrative functions
  * readonly: Read-only access to statistics, dashboards, and personal data
- Token generation, rotation, and revocation through web UI
- Token expiration and automatic cleanup
- API rate limiting and abuse prevention

USER CAPABILITIES AND PERMISSIONS:
Each authenticated user can:
- Create and manage DNS zones (with admin approval)
- Generate and download VPN configurations
- Host websites on clear web, Tor, I2P, and Freenet
- Access personal proxy endpoints with statistics
- View personal usage statistics and dashboards
- Manage API tokens and device configurations

7. WEB UI AND ADMINISTRATION:
ADMINISTRATIVE INTERFACE (/admin/):
- Comprehensive admin dashboard with system overview
- Service management: start, stop, restart, configure all services
- User management: create, modify, delete users, reset passwords
- System configuration: network settings, service parameters, security options
- Database management: backup, restore, migration, connection testing
- Log viewing and analysis with real-time filtering
- Certificate management with automatic Let's Encrypt integration
- Statistics and monitoring dashboards

TOR CONFIGURATION FILES
- /etc/tor/torrc:  default - file localation is changable
- /etc/tor/relayrc: relay only configuration - file localation is changable (we control this process)
- /etc/tor/bridgerc:  bridge only configuration - file localation is changable (we control this process)
- /etc/tor/hiddenrc: hiddenrc - hidden services only configuration - file localation is changable (we control this process)
- /etc/tor/obs4proxyrc: embed ops4proxy - obs4proxy only configuration - file localation is changable (we control this process)


7. WEB UI AND ADMINISTRATION:
ADMINISTRATIVE INTERFACE (/admin/):
- Comprehensive admin dashboard with system overview
- Service management: start, stop, restart, configure all services
- User management: create, modify, delete users, reset passwords
- System configuration: network settings, service parameters, security options
- Database management: backup, restore, migration, connection testing
- Log viewing and analysis with real-time filtering
- Certificate management with automatic Let's Encrypt integration
- Statistics and monitoring dashboards

USER INTERFACE (/user/):
- Personal dashboard with service status and usage statistics
- VPN configuration management with QR code generation
- Website hosting management for all protocols
- DNS zone management with record editing
- API token management with scope selection
- Profile management and password changes

PUBLIC PAGES:
- Status page (/) showing public service availability like Uptime Kuma
- Help documentation (/help/) with setup and usage instructions
- Initial system setup wizard (/setup/) for first-time configuration

UI DESIGN AND ACCESSIBILITY:
- Responsive design working on desktop, tablet, and mobile devices
- Dark theme based on Dracula color scheme with high contrast
- Full accessibility compliance (WCAG 2.1 AA)
- Keyboard navigation support
- Screen reader compatibility
- Route-based URL structure with directory-style URLs (/admin/, /user/, etc.)
- Automatic redirects for missing trailing slashes
- Fast loading with optimized assets and minimal JavaScript

WHITE LABELING AND CUSTOMIZATION:
- Configurable branding through web UI (logo, colors, text)
- Custom hostname and domain configuration
- Customizable email templates and notification text
- Theme customization options
- Custom CSS injection for advanced styling

8. CERTIFICATE MANAGEMENT:
AUTOMATIC TLS CERTIFICATE MANAGEMENT:
- Automatic detection of existing certificates in /etc/letsencrypt/live/domain
- Automatic Let's Encrypt certificate acquisition if certificates missing
- Support for both HTTP-01 and DNS-01 ACME challenges
- DNS provider integration for automated DNS-01 challenges:
  * Cloudflare API integration
  * Namecheap API integration  
  * RFC2136 (BIND) dynamic DNS updates
  * Generic DNS provider plugin system
- Automatic certificate renewal with internal scheduler
- Certificate status monitoring and expiration alerts

CERTIFICATE MONITORING:
- Real-time certificate status display in admin UI
- Certificate expiration warnings (30, 7, 1 day notifications)
- Certificate validation and chain verification
- Multi-domain and wildcard certificate support
- Manual certificate upload and management options

9. NOTIFICATION SYSTEM:
EMAIL NOTIFICATION SYSTEM:
- Comprehensive email notification system for system events
- Rate limiting: one email per event type per configurable timeframe
- Admin-configurable notification intervals and toggles
- Notification categories: service events, certificate expiration, security alerts, system updates
- Email templates for all notification types

NOTIFICATION MANAGEMENT:
- Admin UI for notification configuration and testing
- Notification log table showing all sent notifications with timestamps
- Email delivery status tracking and failure handling
- Notification preference management per user
- Emergency notification bypass for critical security events

NOTIFICATION TYPES:
- Service restart/failure notifications
- DNS zone change notifications  
- Certificate expiration warnings
- Security event alerts (failed logins, suspicious activity)
- System update and maintenance notifications
- User registration and verification notifications

10. STATISTICS AND DASHBOARD:
PUBLIC STATUS DASHBOARD:
- Public status panel similar to Uptime Kuma accessible at root URL (/)
- Service availability indicators (green/yellow/red status)
- Uptime statistics and response times
- Public service endpoints and access information
- No sensitive information exposed (no user counts, detailed stats, internal metrics)

ADMINISTRATIVE DASHBOARD:
- Comprehensive admin dashboard with real-time metrics
- Per-service statistics: CPU usage, RAM consumption, network bandwidth
- VPN statistics: aggregate traffic only (no individual user data)
- DNS statistics: query logs, block count, zone count, popular domains
- Anonymous network statistics: Tor/I2P/Freenet traffic, connections, latency measurements
- System resource monitoring: disk usage, load average, network interfaces

PRIVACY AND DATA RETENTION:
- Smart privacy rules prevent exposure of sensitive statistics publicly
- Log retention: 1 month default with admin override option
- Statistics data retained indefinitely unless manually cleared
- No personally identifiable information in public statistics
- Configurable data retention policies per data type

METRICS COLLECTION:
- All metrics auto-collected internally without external dependencies
- No Prometheus, Grafana, or other external monitoring tools required
- Real-time metrics with configurable collection intervals
- Historical data aggregation and trending
- Export capabilities for external analysis

11. CLI INTERFACE SPECIFICATION:
POSIX-COMPLIANT COMMAND STRUCTURE:
All CLI commands follow strict POSIX conventions with self-explanatory, positional arguments:

SERVICE MANAGEMENT:
casanon start <service>     # Start specific service (tor, i2p, freenet, dns, vpn, proxy, all)
casanon stop <service>      # Stop specific service
casanon restart <service>   # Restart specific service
casanon status [service]    # Show status (all services if no service specified)

CONFIGURATION MANAGEMENT:
casanon config get <key>               # Get configuration value
casanon config set <key> <value>       # Set configuration value
casanon config list                    # List all configuration
casanon config reset                   # Reset to defaults

DNS MANAGEMENT:
casanon dns zone create <domain> <email>    # Create new DNS zone
casanon dns zone delete <domain>            # Delete DNS zone
casanon dns zone list [user]                # List zones (all or by user)
casanon dns record add <zone> <name> <type> <value>  # Add DNS record
casanon dns record delete <zone> <record-id>         # Delete DNS record

USER MANAGEMENT:
casanon user create <email>                    # Create new user
casanon user delete <email>                    # Delete user  
casanon user list                              # List all users
casanon user token create <email> <scope>      # Create API token (admin/global/readonly)
casanon user token revoke <token-id>           # Revoke API token

DATABASE MANAGEMENT:
casanon db migrate <connection-string>         # Migrate to new database
casanon db migrate <connection-string> --test # Test migration without executing
casanon db test-connection <connection-string> # Test database connection
casanon db backup <file>                       # Export database
casanon db restore <file>                      # Import database

SYSTEM MANAGEMENT:
casanon install                # Install system dependencies and setup
casanon server                 # Start web server and all services (main daemon mode)
casanon version               # Show version information
casanon help                  # Show help information

CLI LOGGING AND OUTPUT:
- Unified logging backend with structured log output
- All logs written to /var/log/casanon/*.log with service-specific files
- CLI and web UI always synchronized (same backend logic)
- JSON output option for programmatic usage: --json flag
- Verbose output option: --verbose flag
- Quiet mode for scripts: --quiet flag

12. CONFIGURATION SYSTEM SPECIFICATION:
DATABASE-CENTRIC CONFIGURATION:
- Zero external configuration files used by Casanon itself
- All settings stored in unified database schema
- All generated configuration files (torrc, i2pd.conf, named.conf, wg0.conf, etc.) rebuilt dynamically from database data
- Configuration changes require restart of affected services only
- Atomic configuration updates with rollback capability

CONFIGURATION CATEGORIES:
Service Configuration:
- tor.enabled, tor.port, tor.control_port, tor.exit_relay, tor.bridge_mode
- i2p.enabled, i2p.socks_port, i2p.http_port, i2p.bandwidth_share
- freenet.enabled, freenet.port, freenet.opennet_enabled
- dns.enabled, dns.port, dns.recursion_enabled, dns.forwarders
- vpn.enabled, vpn.port, vpn.network, vpn.dns_servers
- proxy.enabled, proxy.socks_port, proxy.http_port

Network Configuration:
- network.public_hostname, network.wan_interface, network.ipv6_enabled
- network.firewall_enabled, network.block_private_networks

Security Configuration:
- security.session_timeout, security.password_policy, security.rate_limiting
- security.fail2ban_enabled, security.geo_blocking

Email Configuration:
- smtp.host, smtp.port, smtp.user, smtp.password, smtp.tls_mode
- smtp.from_email, smtp.from_name

CONFIGURATION MANAGEMENT:
- Admin UI for all configuration changes with validation
- Configuration export/import functionality (JSON format)
- Configuration versioning and change tracking
- Reset feature: restore all settings except user data to defaults
- Configuration backup included in database backups

13. INSTALLATION AND RUNTIME SPECIFICATION:
GO BUILD SYSTEM:
- Single Go binary compilation with embedded assets
- Cross-compilation for amd64 and arm64 architectures
- All HTML/CSS/JS assets embedded using Go 1.16+ embed directives
- No external build tools required beyond Go toolchain
- Static linking for maximum portability

DEPENDENCY MANAGEMENT:
Automatic installation of required system packages:
- Tor: from distribution repositories or official Tor repositories
- I2Pd: from distribution repositories or official I2Pd repositories

SERVICE INTEGRATION:
- Uses distribution's default service directories and conventions
- No custom mount points, symlinks, or non-standard locations
- DataDir: /var/lib/casanon (database, keys, generated configs)
- LogDir: /var/log/casanon (service logs, audit logs)
- CacheDir: /var/cache/casanon (temporary files, DNS cache)
- ConfigDir: /var/lib/casanon/config (generated service configs)
- Binary location: /usr/local/bin/casanon

STARTUP AND DEPENDENCIES:
- Comprehensive startup checks with automatic dependency installation
- Missing package detection and installation prompts
- Service dependency verification and automatic startup ordering
- Built-in systemd service file generation: casanon --systemd install
- Integration with distribution init systems

14. BUILD SYSTEM SPECIFICATION:
MAKEFILE TARGETS:
make build          # Compile binary with embedded assets
make install        # Install binary and systemd service to system locations
make uninstall      # Remove all installed files and services
make clean          # Clean build artifacts
make test           # Run test suite
make lint           # Run code quality checks

BUILD PROCESS:
- go build compiles everything into single binary
- Assets (HTML/CSS/JS) embedded at compile time using //go:embed
- Version information embedded from git tags
- Build metadata includes commit hash, build date, Go version
- Cross-compilation targets: linux/amd64, linux/arm64

INSTALLATION PROCESS:
make install performs:
1. Copy binary to /usr/local/bin/casanon
2. Create system directories with proper permissions
3. Generate systemd service file
4. Create casanon user and group for service execution
5. Set appropriate file permissions and SELinux contexts
6. Enable systemd service (optional)

15. DATABASE MIGRATION SYSTEM:
LIVE MIGRATION ARCHITECTURE:
- Atomic migration system: all-or-nothing approach with complete rollback
- Test migration mode: --test flag performs complete migration to temporary database without final switchover
- Support for all three database types: SQLite ↔ PostgreSQL ↔ MySQL
- Connection string format support:
  * SQLite: file:/var/lib/casanon/data.db
  * PostgreSQL: postgres://user:pass@host:port/dbname
  * MySQL: user:pass@tcp(host:port)/dbname

MIGRATION PROCESS:
Pre-flight validation:
- Test target database connection and permissions
- Verify schema compatibility between database types
- Check available disk space (requires ~2x source database size)
- Validate data type conversion compatibility
- Ensure no blocking processes on target database

Shadow database approach:
- Create complete shadow copy of database on target system
- Copy all data while source system continues operating
- Incremental synchronization of changes during copy
- Brief read-only mode (1-2 seconds) for final synchronization
- Atomic switchover only after complete validation

Migration safety features:
- Original database never modified until final switchover
- Shadow database automatically dropped on any failure
- Complete rollback capability at any point before switchover
- Comprehensive validation of migrated data integrity
- Row count verification, foreign key validation, sample data comparison

TEST MIGRATION MODE:
casanon db migrate "postgres://user:pass@host/db" --test
- Performs complete migration to temporary test database
- Validates all data integrity and performance characteristics
- Generates detailed migration report with timing estimates
- Estimates actual downtime for real migration
- Provides performance comparison between source and target
- Test database automatically cleaned up after completion
- No impact on production system

16. LOGGING AND MONITORING:
LOGGING ARCHITECTURE:
- Unified logging backend for all components
- Structured logging with JSON format option
- Log levels: DEBUG, INFO, WARN, ERROR, FATAL
- Service-specific log files in /var/log/casanon/services/
- Automatic log rotation with configurable retention

LOG FILES:
/var/log/casanon/casanon.log          # Main application log
/var/log/casanon/services/tor.log     # Tor service log
/var/log/casanon/services/i2p.log     # I2P service log  
/var/log/casanon/services/freenet.log # Freenet service log
/var/log/casanon/services/dns.log     # DNS service log
/var/log/casanon/services/vpn.log     # VPN service log
/var/log/casanon/services/proxy.log   # Proxy service log
/var/log/casanon/web.log              # Web server access log
/var/log/casanon/audit.log            # Security audit log

MONITORING AND HEALTH CHECKS:
- Automatic health checks every 30 seconds for all services
- Service restart on health check failure
- Resource monitoring: CPU, memory, disk, network per service
- Performance metrics collection and storage
- Alert system for service failures and resource exhaustion

17. SECURITY SPECIFICATIONS:
SECURITY ARCHITECTURE:
- Defense in depth with multiple security layers
- Principle of least privilege for all components
- Secure defaults with optional hardening
- Regular security updates and vulnerability scanning

AUTHENTICATION AND AUTHORIZATION:
- Secure password hashing using bcrypt with high work factor
- Session management with secure, httpOnly cookies
- CSRF protection on all state-changing operations
- Rate limiting on authentication endpoints
- Account lockout after failed login attempts

NETWORK SECURITY:
- Automatic firewall rule management
- Private network routing blocked by default
- Secure service bindings and port management
- TLS/SSL for all web communications
- Certificate pinning for service communications

DATA PROTECTION:
- Database encryption at rest (optional)
- Secure key storage and management
- Privacy-preserving logging and analytics
- GDPR compliance features for EU users
- Data retention controls and automatic purging

18. LICENSE AND DOCUMENTATION:
LICENSE:
- MIT License for maximum compatibility and adoption
- Clear copyright attribution
- Commercial use permitted
- Distribution and modification permitted

DOCUMENTATION REQUIREMENTS:
README.md:
- Project overview and feature summary
- Quick start installation guide
- Basic usage examples
- Link to full documentation

INSTALLATION.md:
- Detailed installation instructions for all supported distributions
- Dependency requirements and installation
- Initial setup and configuration
- Troubleshooting common installation issues

CONFIGURATION.md:
- Complete configuration reference
- Service-specific configuration options
- Advanced configuration scenarios
- Security configuration recommendations

API.md:
- Complete API documentation
- Authentication and authorization
- All endpoints with request/response examples
- Rate limiting and usage guidelines

19. FINAL IMPLEMENTATION REQUIREMENTS:
CODE QUALITY:
- Comprehensive error handling with meaningful error messages
- Unit tests for all critical functionality
- Integration tests for service interactions
- Code documentation with Go doc conventions
- Consistent code style with gofmt and golint

PERFORMANCE:
- Efficient resource usage with minimal memory footprint
- Fast startup time (< 5 seconds on modern hardware)
- Responsive web interface (< 200ms page load)
- Optimized database queries and indexes
- Connection pooling and resource management

RELIABILITY:
- Graceful service degradation on component failure
- Automatic recovery from transient failures
- Data integrity protection and validation
- Backup and restore functionality
- Disaster recovery procedures

MAINTENANCE:
- Automated update system with rollback capability
- Configuration validation and error reporting
- Health monitoring and alerting
- Performance profiling and optimization tools
- Comprehensive logging for troubleshooting

This specification defines every aspect of the Casanon project required for complete implementation. Any AI system receiving this specification should be able to generate all source code, configuration files, documentation, build scripts, and installation procedures without requiring additional clarification or specification details.
```

