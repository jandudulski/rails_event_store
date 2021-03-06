GEM_VERSION  = $(shell cat ../RES_VERSION)
GEM_NAME     = rails_event_store_active_record
REQUIRE      = $(GEM_NAME)
IGNORE       = RailsEventStoreActiveRecord::IndexViolationDetector\#detect
SUBJECT      ?= RailsEventStoreActiveRecord*
DATABASE_URL ?= sqlite3::memory:

install: ## Install gem dependencies
	@echo "Installing gem dependencies"
	@bundle install
	@echo "Installing v1_v2_schema_migration gem dependencies"
	@BUNDLE_GEMFILE=spec/v1_v2_schema_migration/Gemfile bundle install
	@BUNDLE_GEMFILE=spec/without_rails/Gemfile bundle install

remove-lock:
	@echo "Removing resolved dependency versions"
	-rm Gemfile.lock
	-rm spec/v1_v2_schema_migration/Gemfile.lock
	-rm spec/without_rails/Gemfile.lock

reinstall: remove-lock install ## Removing resolved dependency versions


test: ## Run unit tests
	@echo "Running unit tests"
	@BUNDLE_GEMFILE=Gemfile bundle exec rspec

mutate: test ## Run mutation tests
	@echo "Running mutation tests"
	@BUNDLE_GEMFILE=Gemfile DATABASE_URL=$(DATABASE_URL) bundle exec mutant --include lib \
		$(addprefix --require ,$(REQUIRE)) \
		$(addprefix --ignore-subject ,$(IGNORE)) \
		--use rspec "$(SUBJECT)"

mutate-fast: ## Run mutation tests with --fail-fast
	@echo "Running mutation tests with --fail-fast"
	@BUNDLE_GEMFILE=Gemfile DATABASE_URL=$(DATABASE_URL) bundle exec mutant --include lib \
		$(addprefix --require ,$(REQUIRE)) \
		$(addprefix --ignore-subject ,$(IGNORE)) \
		--fail-fast \
		--use rspec "$(SUBJECT)"

build:
	@echo "Building gem package"
	@gem build -V $(GEM_NAME).gemspec
	@mkdir -p pkg/
	@mv $(GEM_NAME)-$(GEM_VERSION).gem pkg/

push:
	@echo "Pushing package to RubyGems"
	@gem push pkg/$(GEM_NAME)-$(GEM_VERSION).gem

clean:
	@echo "Removing previously built package"
	-rm pkg/$(GEM_NAME)-$(GEM_VERSION).gem

help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

.PHONY: help
.DEFAULT_GOAL := help

