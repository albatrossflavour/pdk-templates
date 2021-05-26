.PHONY: clean
.DEFAULT: validate

PDK=/opt/puppetlabs/pdk/bin/pdk

all:
	${MAKE} test

test:
	${MAKE} setup
	${MAKE} validate
	${MAKE} unit
	${MAKE} acceptance
	${MAKE} documentation

release:
	@${MAKE} test
	@${MAKE} release_updates

setup:
	@${PDK} bundle install

validate:
	@${PDK} bundle exec rake metadata_lint
	@${PDK} bundle exec rake syntax
	@${PDK} bundle exec rake validate
	@${PDK} bundle exec rake rubocop
	@${PDK} bundle exec rake lint
	@${PDK} bundle exec rake check:git_ignore
	@${PDK} bundle exec rake check:dot_underscore
	@${PDK} bundle exec rake check:symlinks
	@${PDK} bundle exec puppet-lint manifests

unit:
	@${PDK} bundle exec rake spec

acceptance:
	@${MAKE} test_puppet6
	@${MAKE} test_puppet7

test_puppet6:
	@${PDK} bundle exec rake 'litmus:provision_list[release_tests]'
	@${PDK} bundle exec rake litmus:install_agent[puppet6]
	@${PDK} bundle exec rake litmus:install_module
	@${PDK} bundle exec rake litmus:acceptance:parallel
	@${PDK} bundle exec rake litmus:tear_down

test_puppet7:
	@${PDK} bundle exec rake 'litmus:provision_list[release_tests]'
	@${PDK} bundle exec rake litmus:install_agent[puppet7]
	@${PDK} bundle exec rake litmus:install_module
	@${PDK} bundle exec rake litmus:acceptance:parallel
	@${PDK} bundle exec rake litmus:tear_down

teardown:
	@${PDK} bundle exec rake litmus:tear_down

documentation:
	@${PDK} bundle exec puppet strings generate --format=markdown

release_updates:
	@${MAKE} documentation
	@${PDK} bundle exec rake module:bump:minor
	@${PDK} bundle exec rake changelog
	@${PDK} bundle exec rake module:tag
	@${PDK} bundle exec rake build

clean:
	@${PDK} bundle exec rake module:clean
