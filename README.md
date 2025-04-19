# Ansible Role: native_package

This role installs or removes packages using the native package manager from direct URLs. It differs from the built-in Ansible package management by supporting direct downloads from URLs, particularly for `.deb`, `.rpm`, and `.apk` files.

## Requirements

- Ansible 2.9 or higher
- Administrative privileges on target hosts
- Internet connectivity to download packages (for installation)

## Role Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `package_urls` | Dictionary mapping package type (`deb`, `rpm`, `apk`) to details. Each package type key must contain: <ul><li>`name`: The name of the package for installation/removal (OS-family specific).</li><li>Architecture keys (`x86`, `ia64`, `arm`, `aarch64`) mapping to the direct download URL for the package file (required for installation).</li></ul> The role selects the appropriate URL and name based on the target OS family and architecture. | (required) |
| `installed` | Whether to install (`true`) or remove (`false`) the package | `true` |

## Example Playbooks

### Installing a package

```yaml
- hosts: servers
  roles:
    - role: native_package
      vars:
        package_urls:
          deb:
            name: google-chrome-stable # Name for Debian/Ubuntu
            ia64: https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
            # aarch64: https://example.com/chrome_arm64.deb
          rpm:
            name: google-chrome-stable # Name for RHEL/CentOS/Fedora
            ia64: https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm
          apk:
            name: google-chrome # Example name for Alpine
            ia64: https://example.com/path/to/chrome_x86_64.apk
            # aarch64: https://example.com/path/to/chrome_arm64.apk
        installed: true # Default, can be omitted
```

### Removing a previously installed package

```yaml
- hosts: servers
  roles:
    - role: native_package
      vars:
        package_urls: # Required to provide the package name for removal
          deb:
            name: google-chrome-stable
          rpm:
            name: google-chrome-stable
          apk:
            name: google-chrome
          # URLs are not needed for removal, only the 'name' under the relevant OS family key(s)
        installed: false
```

## Supported Package Formats

- `.deb` - Debian/Ubuntu based systems
- `.rpm` - RedHat/CentOS/Fedora based systems
- `.apk` - Alpine Linux based systems

## License

MIT