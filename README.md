# OS Privacy Lab

Research projects and experiments involving operating-system identifiers, fingerprinting vectors, and privacy-hardening techniques across Linux systems.

This repository focuses on how operating systems and software expose persistent identifiers, how those identifiers may contribute to system fingerprinting or tracking, and what practical techniques can reduce that exposure without unnecessarily disrupting normal system operation.

The goal is to investigate these mechanisms, implement practical privacy experiments, measure their effects, and document their results, trade-offs, and limitations.

These projects are experimental and are intended for educational, research, and personal-use purposes.

---

## Projects

### 1. Machine-ID Rotation

#### Summary

The `machine-id` is a persistent identifier used by Linux systems and software to identify a particular installation or system instance.

This project investigates the privacy implications of persistent machine identifiers and evaluates boot-time rotation as one possible method of reducing persistence of that particular identifier.

The experiment currently covers both:

* **Artix Linux using OpenRC**
* **Arch Linux using systemd**

The implementations use a common machine-ID rotation script, while the init-system-specific service files are responsible for executing that script at boot.

On the systems tested by this project, the machine ID is maintained in:

* `/etc/machine-id`
* `/var/lib/dbus/machine-id`

The purpose of the experiment is to determine how changing these identifiers affects system behavior and to better understand the relationship between persistent OS identifiers, DBus, desktop environments, and other system services.

#### Objectives

* Identify where Linux systems store `machine-id`
* Understand how system components such as DBus and desktop environments use `machine-id`
* Evaluate the privacy implications of persistent OS identifiers
* Implement a boot-time machine-ID rotation mechanism
* Support multiple init systems using init-system-specific service files
* Measure system behavior before and after rotation
* Document results, trade-offs, and limitations
* Determine whether boot-time rotation provides a meaningful reduction in persistence of this particular identifier

---

## Implementation

Project directory:

```text
machine-id-rotation/
├── scripts/
│   └── rotate-machine-id
└── service-files/
    ├── openrc/
    │   └── rotate-machine-id
    └── systemd/
        └── rotate-machine-id.service
```

### Boot-time rotation script

Path:

`machine-id-rotation/scripts/rotate-machine-id`

The common script generates a new random 32-character hexadecimal identifier and applies it to the machine-ID locations used by the tested systems.

The script intentionally does **not** maintain a history or log of previous identifiers. Retaining a record of rotated identifiers would unnecessarily preserve information that this privacy experiment is specifically intended not to retain.

### OpenRC implementation

Path:

`machine-id-rotation/service-files/openrc/rotate-machine-id`

The OpenRC service executes the common rotation script during system startup.

This implementation was originally developed and tested on Artix Linux with the OpenRC init system and KDE Plasma.

### systemd implementation

Path:

`machine-id-rotation/service-files/systemd/rotate-machine-id.service`

The systemd service executes the same common rotation script during system startup.

This implementation was developed and tested on Arch Linux with systemd and KDE Plasma.

The use of separate service files allows the same underlying experiment to be applied across different init systems without maintaining separate copies of the rotation logic.

---

## Important Considerations

Changing an operating-system identifier can affect software that expects that identifier to remain persistent.

Potential effects depend on the operating system, desktop environment, services, applications, and configuration in use.

Particular areas of interest include:

* DBus
* KDE Plasma
* Desktop applications
* Network-related services
* Software that uses machine-specific identifiers
* Applications that use machine-ID as part of local state or identification

Machine-ID rotation should therefore be treated as an experiment rather than a universally recommended configuration.

This project does not claim that rotating `machine-id` eliminates system fingerprinting or tracking. It addresses only one potential source of persistent system identification.

---

## Notes

The project is designed to support multiple Linux init systems.

The rotation logic is intentionally shared between implementations, while OpenRC and systemd use their own service definitions to control when the script executes.

The current implementations are research configurations and may require modification for other Linux distributions, init systems, desktop environments, or system configurations.

---

## No Warranty / No Guarantee

This repository is provided as-is, without any warranty, guarantee, or promise of fitness for any particular purpose.

All experiments are intended for research and educational use only.

Use at your own risk.

Modifying OS-level identifiers may affect system behavior depending on operating-system configuration and installed software.

---

## Future Work / Plans

Planned or potential expansions include:

* Hostname rotation experiments
* DHCP client ID randomization
* MAC address randomization
* KDE telemetry analysis
* Browser fingerprinting experiments
* Additional Linux distributions and init systems
* Additional research into persistent operating-system identifiers
* Comparative testing of privacy-hardening techniques
* Documentation of compatibility issues and observed side effects

---

## License / Use Allowance

You are free to:

* Use
* Modify
* Study
* Adapt
* Reproduce
* Share

the code and documentation in this repository for personal, educational, or research purposes.

Attribution is required.

Commercial use is not allowed, and no warranty or guarantee is provided.
