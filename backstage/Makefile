.PHONY: log clean-all clean-deps install build-ts build test start reset all

# Simple logger function
define log
 @echo "$(shell date '+%Y-%m-%d %H:%M:%S') $(1)"
endef

clean-yarnlock:
 $(call log,🧹 Emptying yarn.lock...)
 @truncate -s 0 yarn.lock || true
 $(call log,✅ Clean completed)

clean-all:
 $(call log,🧹 Emptying yarn.lock...)
 @truncate -s 0 yarn.lock || true
 $(call log,🗑️ Deleting all node_modules...)
 @find . -name "node_modules" -type d -prune -exec rm -rf '{}' +
 $(call log,✅ Clean completed)

# 🧹 Clean lock + node_modules
clean-deps:
 $(call log,🗑️ Deleting all node_modules...)
 @find . -name "node_modules" -type d -prune -exec rm -rf '{}' +
 $(call log,✅ Clean completed)

# 📦 Install dependencies
install:
 $(call log,📦 Running yarn install...)
 @yarn install
 $(call log,✅ Install completed)

# 🔧 TypeScript compile
build-ts:
 $(call log,📦 Running yarn tsc...)
 @yarn tsc
 $(call log,✅ TypeScript build completed)

# 🏗️ Full build
build:
 $(call log,🏗️ Running yarn build:all...)
 @yarn build:all
 $(call log,✅ Build completed)

# 🧪 Run tests
test:
 $(call log,🧪 Running yarn test:all...)
 @yarn test:all
 $(call log,✅ Tests completed)

# 🚀 Start Backstage (kills ports first)
start:
 $(call log,🚀 Killing ports 7007 & 3000...)
 @lsof -ti:7007 | xargs -r kill -9 || true
 @lsof -ti:3000 | xargs -r kill -9 || true
 $(call log,🚀 Starting Backstage...)
 @yarn start


# 🚀 Stop Backstage (kills ports first)
stop:
 $(call log,🚀 Stopping local backstage by killing ports 7007 & 3000...)
 @lsof -ti:7007 | xargs -r kill -9 || true
 @lsof -ti:3000 | xargs -r kill -9 || true


# Wait for Backstage to become available
wait-for-app:
 $(call log,⏳ Waiting for app on port 7007...)
 @until curl -s http://localhost:7007 > /dev/null; do \
  sleep 2; \
  echo "Waiting..."; \
 done
 $(call log,✅ App is up)

# Start app only if not running
ensure-started:
 @if curl -s http://localhost:7007 > /dev/null; then \
  echo "✅ App already running"; \
 else \
  echo "❌ App not running"; \
  echo "🧹 Killing stale processes"; \
  lsof -ti:7007 | xargs -r kill -9 || true; \
  lsof -ti:3000 | xargs -r kill -9 || true; \
  echo "🚀 Starting app"; \
  yarn start > backstage.log 2>&1 & \
  sleep 5; \
 fi

# Run Cypress
cypress-run:
 $(call log,🧪 Running Cypress...)
 @yarn cypress run --browser chrome --headless --config-file ./cypress-local.config.js --spec 'cypress/e2e/*.js' --reporter-options reportDir=cypress/reports  || { echo 'Cypress run with reports failed'; exit 1; }
 $(call log,🧪 Ended Cypress run...)

# Run Cypress lite
cypress-run-lite:
 $(call log,🧪 Running Cypress on some files, not all...)
 @yarn cypress run --browser chrome --headless --config-file ./cypress-local.config.js --spec 'cypress/e2e/c*.js' --reporter-options reportDir=cypress/reports  || { echo 'Cypress run with reports failed'; exit 1; }
 $(call log,🧪 Ended Cypress run on some files...)
# Full E2E flow
e2e: ensure-started wait-for-app cypress-run


# 🔄 Full reset (clean + install + start)
reset: clean install start

# 🧱 Full pipeline (what CI/CD would run)
all: clean-all install build-ts build test start


#1 🔹 Start app
 # make start

#2 🔹 Clean everything
 # make clean-all

#3 🔹 Full rebuild
 # make reset

#4 🔹 CI-style pipeline
 # make all

#5 🔹 Run test
 # make test
 