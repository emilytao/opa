DEPLOY_PRIME_URL ?= "http://localhost:8888"

######################################################
#
# Development targets
#
######################################################

.PHONY: clean
clean:
	rm -rf ${CURDIR}/website/data/releases.yaml
	rm -rf $(CURDIR)/website/generated
	rm -rf $(CURDIR)/website/public
	rm -rf $(CURDIR)/website/resources

.PHONY: generate
generate:
	$(CURDIR)/website/scripts/load-docs.sh

# The website has some npm dependencies saved in ./website/node_modules
# As well as dependencies required for the "live-blocks".
# Use this target to update them as needed.
.PHONY: install-deps
install-deps: live-blocks-install-deps
	npm install

# The live-blocks-% pattern target will shim to the
# npm scripts in ./website/scripts/live-blocks
.PHONY: live-blocks-%
live-blocks-%:
	cd $(CURDIR)/website/scripts/live-blocks && npm run $*

.PHONY: serve-local
serve-local: production-build
	# must be run from root of repo for
	# the netlify.toml config to work
	cd $(CURDIR)/.. && netlify dev --offline

.PHONY: serve-remote
serve-remote: production-build
	# must be run from root of repo for
	# the netlify.toml config to work
	cd $(CURDIR)/.. && netlify deploy

######################################################
#
# CI targets
#
######################################################

.PHONY: hugo-production-build
hugo-production-build:
	hugo \
		--source $(CURDIR)/website \
		--contentDir generated \
		--ignoreCache \
		--minify

.PHONY: production-build
production-build: clean generate hugo-production-build live-blocks-inject

.PHONY: preview-build
preview-build:
	hugo \
		--source $(CURDIR)/website \
		--contentDir generated \
		--baseURL $(DEPLOY_PRIME_URL) \
		--buildDrafts \
		--buildFuture \
		--ignoreCache
	make live-blocks-inject
