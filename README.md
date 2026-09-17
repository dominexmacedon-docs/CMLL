# CMLL

CMLL is the command line language which is suitable for command line running with multiple lines and executing things on the fly.

## Installation

CMLL provides a Linux x86_64 release that can be installed directly into the system's executable directory.

### Requirements

* Linux x86_64
* `curl`
* `unzip`
* `sudo`

### Install

Create or use the installation `Makefile` in the project directory:

```makefile
CMLL_VERSION := cmll-v1.0.0
CMLL_URL := https://github.com/dominexmacedon-docs/CMLL/releases/download/$(CMLL_VERSION)/cmll-linux-x86_64.zip

INSTALL_DIR := /usr/local/bin
BINARY := cmll
ZIP := cmll-linux-x86_64.zip

.PHONY: install uninstall clean

install:
	@echo "Downloading CMLL $(CMLL_VERSION)..."
	curl -L "$(CMLL_URL)" -o "$(ZIP)"
	@echo "Extracting CMLL..."
	unzip -o "$(ZIP)"
	@echo "Installing CMLL to $(INSTALL_DIR)..."
	sudo install -m 755 "$(BINARY)" "$(INSTALL_DIR)/$(BINARY)"
	@echo "CMLL installed successfully."
	@echo "Run: cmll"

uninstall:
	@echo "Removing CMLL..."
	sudo rm -f "$(INSTALL_DIR)/$(BINARY)"
	@echo "CMLL removed."

clean:
	rm -f "$(ZIP)" "$(BINARY)"
```

Run:

```bash
make install
```

The CMLL executable will be installed as:

```text
/usr/local/bin/cmll
```

Because `/usr/local/bin` is normally included in the system `PATH`, CMLL can then be executed from any directory:

```bash
cmll
```

## Uninstall

To remove CMLL from the system:

```bash
make uninstall
```

## Clean Installation Files

To remove the downloaded ZIP and local executable:

```bash
make clean
```

## Project

CMLL is designed around command-line programming, with support for multiple lines and executing things on the fly.

## Release

CMLL Linux x86_64 release:

```text
https://github.com/dominexmacedon-docs/CMLL/releases/download/cmll-v1.0.0/cmll-linux-x86_64.zip
```
