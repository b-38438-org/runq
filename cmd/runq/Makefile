include ../../make.rules

export GOPATH := $(shell go env GOPATH | cut -d: -f1)

RUNC_REV := v1.0.0-rc7
RUNC_PATH := $(GOPATH)/src/github.com/opencontainers/runc
EXTRA_LDFLAGS := "-X main.runqCommit=$(GIT_COMMIT)"

runq: $(RUNC_PATH)/.git $(shell find . -type f ! -name runq)
	cd $(RUNC_PATH) && git clean -fd
	cd $(RUNC_PATH) && git checkout -q -f $(RUNC_REV)
	cd $(RUNC_PATH) && git apply $(CURDIR)/runc-to-runq.patch
	cp $(CURDIR)/runq.go $(RUNC_PATH)/
	CC=gcc EXTRA_LDFLAGS=$(EXTRA_LDFLAGS) $(MAKE) -C $(RUNC_PATH) BUILDTAGS="" runc
	cp -f $(RUNC_PATH)/runc $(CURDIR)/runq

$(RUNC_PATH)/.git:
	@echo Error: RunC source not found
	@echo To download RunC source: '"go get -d github.com/opencontainers/runc"'; false

install: runq
	install -m 0755 -D $(CURDIR)/runq $(RUNQ_ROOT)/runq

clean:
	cd $(RUNC_PATH) && git clean -xfd && git reset --hard && git checkout master
	rm -f runq
