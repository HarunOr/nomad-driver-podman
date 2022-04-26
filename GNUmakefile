SHELL = bash

default: help

HELP_FORMAT="    \033[36m%-25s\033[0m %s\n"
.PHONY: help
help: ## Display this usage information
	@echo "Valid targets:"
	@grep -E '^[^ ]+:.*?## .*$$' $(MAKEFILE_LIST) | \
		sort | \
		awk 'BEGIN {FS = ":.*?## "}; \
			{printf $(HELP_FORMAT), $$1, $$2}'
	@echo ""

.PHONY: changelogfmt
changelogfmt: ## Format changelog GitHub links
	@echo "--> Making [GH-xxxx] references clickable..."
	@sed -E 's|([^\[])\[GH-([0-9]+)\]|\1[[GH-\2](https://github.com/hashicorp/nomad-driver-podman/issues/\2)]|g' CHANGELOG.md > changelog.tmp && mv changelog.tmp CHANGELOG.md

.PHONY: check
check: ## Lint the source code
	@echo "==> Linting source code ..."
	@$(CURDIR)/build/bin/golangci-lint run
	# hclogvet is busted; turn off for now
	# @echo "==> vetting hc-log statements"
	# @$(CURDIR)/build/bin/hclogvet $(CURDIR)

.PHONY: deps
deps: ## Install build dependencies
	@echo "==> Installing build dependencies ..."
	GOBIN=$(CURDIR)/build/bin go install github.com/golangci/golangci-lint/cmd/golangci-lint@v1.45.2
	GOBIN=$(CURDIR)/build/bin go install github.com/client9/misspell/cmd/misspell@v0.3.4
	GOBIN=$(CURDIR)/build/bin go install github.com/hashicorp/go-hclog/hclogvet@latest
	GOBIN=$(CURDIR)/build/bin go install gotest.tools/gotestsum@v1.8.0

.PHONY: clean
clean: ## Cleanup previous build
	@echo "==> Cleanup previous build"
	rm -f ./build/nomad-driver-podman

.PHONY: build
build: clean deps check build/nomad-driver-podman ## Build the nomad-driver-podman plugin

build/nomad-driver-podman:
	@echo "==> Building driver plugin ..."
	mkdir -p build
	go build -o build/nomad-driver-podman .

.PHONY: test
test: ## Run unit tests
	@echo "==> Running unit tests ..."
	go test -v -race ./...

.PHONY: version
version: ## Get the current version string
	@$(CURDIR)/scripts/version.sh version/version.go
