# Source Code from: https://github.com/aws/eks-charts
# Run `make install-tools`, then `make all` for chart validation test and lint
REPO_ROOT ?= $(shell git rev-parse --show-toplevel)

.PHONY:all
all: validate lint

install-tools:
	${REPO_ROOT}/charts/adot-exporter-for-eks-on-ec2/scripts/install-tools.sh

.PHONY: validate
validate:
	${REPO_ROOT}/charts/adot-exporter-for-eks-on-ec2/scripts/validate-charts.sh

.PHONY: lint
lint:
	${REPO_ROOT}/charts/adot-exporter-for-eks-on-ec2/scripts/lint-charts.sh

.PHONY: clean
clean:
	rm -rf ${REPO_ROOT}/build/
