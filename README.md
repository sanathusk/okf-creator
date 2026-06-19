# OKF Creator Project

## Overview
This repository contains the **OKF Creator** tooling, which helps you create, manage, and maintain Open Knowledge Format (OKF) bundles — portable markdown files with YAML frontmatter that serve as structured knowledge catalogs.

## Features
- Scaffold new knowledge bundles
- Validate existing bundles against the OKF schema
- Organize and version-control concept documentation
- Progressive disclosure of detailed information via markdown sections
- Centralized metadata catalogs for easy discovery

## Getting Started
1. **Clone the repository**
   ```bash
   git clone https://github.com/your-org/okf-creator.git
   cd okf-creator
   ```

2. **Install dependencies**
   ```bash
   # If using Node.js
   npm install
   ```

3. **Run the tool**
   Follow the commands provided in the individual skill scripts (e.g., `okf-creator init`, `okf-creator validate`, etc.).

## Usage Examples
```bash
# Create a new knowledge bundle
okf-creator init my-concept-doc

# Validate a bundle
okf-creator validate path/to/bundle

# Generate documentation site
okf-creator build --output ./site
```

## Contributing
Contributions are welcome! Please read our [Contributing Guide](CONTRIBUTING.md) for details on how to get involved.

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for more information.

---

*Generated with ❤️ by the OKF Creator team*