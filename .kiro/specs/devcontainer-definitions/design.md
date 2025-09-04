# Design Document

## Overview

This design outlines the architecture for creating devcontainer definition files for personal development projects. The system will generate standardized, modular devcontainer configurations that support JavaScript/TypeScript web development initially, with a foundation for future expansion to Python, Tauri, Flutter, and Java environments.

## Architecture

### Core Components

1. **Template Generator**: Creates devcontainer.json files based on predefined templates
2. **Configuration Manager**: Handles common tools and environment setup
3. **Extension Manager**: Manages VS Code extensions and customizations
4. **Documentation Generator**: Creates setup and customization documentation

### Design Principles

- **Modularity**: Each environment type is self-contained but shares common components
- **Extensibility**: Easy addition of new development environments
- **Consistency**: Standardized structure across all devcontainer types
- **Customizability**: Clear paths for user customization and extension

## Components and Interfaces

### 1. Template Structure

```
.devcontainer/
├── devcontainer.json          # Main configuration
├── Dockerfile                 # Custom Fedora-based image
└── docker-compose.yml         # Optional for multi-service setups
```

### 2. JavaScript/TypeScript Web Development Template

#### Base Configuration
- **Base Image**: Custom Dockerfile with Fedora base (`fedora:latest`)
- **Package Manager**: dnf (Fedora's package manager)
- **Node.js**: Managed via NVM (Node Version Manager) for easy version switching
- **Python**: Managed via UV (Python Version Manager) for easy version switching
- **Package Managers**: npm (default), yarn and pnpm available via npm install

#### Development Tools
- **Testing**: Jest, Vitest
- **Code Quality**: ESLint, Prettier
- **VS Code Extensions**:
  - `dbaeumer.vscode-eslint`
  - `esbenp.prettier-vscode`
  - `ms-vscode.vscode-typescript-next`
  - `bradlc.vscode-tailwindcss` (optional)

#### Dockerfile Configuration
```dockerfile
FROM fedora:latest

# Install basic development tools
RUN dnf update -y && \
    dnf install -y git curl wget unzip sudo python3 python3-pip && \
    dnf clean all

# Create non-root user
RUN useradd -m -s /bin/bash vscode && \
    echo 'vscode ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

USER vscode
WORKDIR /home/vscode

# Install NVM (Node Version Manager)
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash && \
    echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.bashrc && \
    echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ~/.bashrc && \
    echo '[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"' >> ~/.bashrc

# Install UV (Python Version Manager)
RUN curl -LsSf https://astral.sh/uv/install.sh | sh && \
    echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.bashrc

# Set default Node.js version via NVM
RUN bash -c 'source ~/.nvm/nvm.sh && nvm install 20 && nvm use 20 && nvm alias default 20'
```

### 3. Common Development Tools Module

#### Git Configuration
- Pre-configured Git with basic settings
- Basic Git functionality without additional extensions

#### Shell Environment
- Default bash shell (lightweight)
- Basic aliases and environment variables
- Simple prompt configuration

#### Utilities
- curl, wget, unzip
- Docker CLI (for Docker-outside-of-Docker scenarios)
- Basic development utilities

#### VS Code Essential Extensions
- `ms-azuretools.vscode-docker`
- `ms-vscode-remote.remote-containers`

### 4. Extension and Customization System

#### Template Customization Points
1. **Environment Variables**: Configurable through devcontainer.json
2. **Port Forwarding**: Configurable ports for different services
3. **Post-Create Commands**: Customizable setup scripts
4. **Additional Features**: Modular feature additions

#### Service Integration Support
- Database services (PostgreSQL, MySQL, MongoDB)
- Redis for caching
- Docker Compose integration for multi-service setups

## Data Models

### DevContainer Configuration Schema

```typescript
interface DevContainerConfig {
  name: string;
  image?: string;
  dockerfile?: string;
  features: Record<string, FeatureConfig>;
  customizations: {
    vscode: {
      extensions: string[];
      settings?: Record<string, any>;
    };
  };
  forwardPorts?: number[];
  postCreateCommand?: string;
  remoteUser?: string;
  remoteEnv?: Record<string, string>;
}

interface FeatureConfig {
  version?: string;
  [key: string]: any;
}
```

### Template Metadata

```typescript
interface TemplateMetadata {
  id: string;
  name: string;
  description: string;
  category: 'web' | 'mobile' | 'desktop' | 'backend';
  language: string[];
  frameworks?: string[];
  services?: string[];
  complexity: 'basic' | 'intermediate' | 'advanced';
}
```

## Error Handling

### Configuration Validation
- JSON schema validation for devcontainer.json
- Feature compatibility checking
- Image availability verification

### Runtime Error Handling
- Graceful fallback for missing features
- Clear error messages for configuration issues
- Automatic retry mechanisms for network-dependent operations

### User Feedback
- Detailed logging during container creation
- Progress indicators for long-running operations
- Clear documentation for troubleshooting common issues

## Testing Strategy

### Unit Testing
- Template generation logic
- Configuration validation
- Feature integration tests

### Integration Testing
- Full devcontainer creation and startup
- VS Code extension loading
- Service connectivity (databases, etc.)

### End-to-End Testing
- Complete development workflow testing
- Multi-service environment testing
- Cross-platform compatibility (Windows, macOS, Linux)

### Test Environments
- Local Docker testing
- GitHub Codespaces compatibility
- Various VS Code versions

## Implementation Phases

### Phase 1: JavaScript/TypeScript Foundation
1. Basic JavaScript/TypeScript template
2. Common tools integration
3. Essential VS Code extensions
4. Documentation and customization guides

### Phase 2: Service Integration
1. Database service templates (PostgreSQL, MySQL)
2. Docker Compose integration
3. Port forwarding configuration
4. Environment variable management

### Phase 3: Future Environment Support
1. Python development environment
2. Tauri (Rust-based) desktop development
3. Flutter mobile development
4. Java development environment

### Phase 4: Advanced Features
1. Multi-language project support
2. Microservices development templates
3. CI/CD integration templates
4. Performance optimization tools

## Security Considerations

### Container Security
- Use official Microsoft devcontainer images
- Regular base image updates
- Minimal privilege principles

### Development Security
- Secure default configurations
- Environment variable best practices
- Secret management guidelines

### Network Security
- Controlled port exposure
- Secure service-to-service communication
- VPN and proxy support documentation

## Performance Optimization

### Container Startup
- Optimized base images
- Efficient layer caching
- Parallel feature installation

### Development Experience
- Fast file synchronization
- Optimized extension loading
- Efficient resource utilization

### Scalability
- Modular template system
- Efficient template generation
- Caching mechanisms for repeated operations