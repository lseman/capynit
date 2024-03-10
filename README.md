# capynit

capynit is a custom initramfs solution designed to offer a lightweight and secure environment for early boot stages. Featuring a statically compiled Bash shell with musl, the full core utilities derived from BSD (via Chimera Linux's chimerautils), and encryption support, Capynit is tailored for systems requiring fast early user-space functionalities.

## Features

- **Static Bash Shell**: Utilizes a musl-based static compilation of Bash for maximum compatibility and reliability.
- **BSD Core Utilities**: Leverages Chimera Linux's chimerautils, offering a complete set of BSD-derived core utilities for Linux.
- **Encryption Support**: Built-in mechanisms to support encrypted root file systems, ensuring security from the earliest boot stages.
- **Lightweight and Fast**: Optimized for performance, providing a fast boot process without compromising functionality.

## Dependencies

Capynit relies on several dependencies to offer its full range of features:

- `pax-utils`: Essential for binary analysis and ELF file manipulation.

## Customization

Capynit supports encryption out of the box. If your setup involves encrypted volumes, make sure the necessary utilities are included and configured correctly in the initramfs.

## License

Capynit is licensed under the GPL. See the LICENSE file for more details.
