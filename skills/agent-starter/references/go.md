# Go HTTP API

Use this profile for a Go API or when the user chooses Go for a separate backend. Recommend a single Go module with standard `net/http` for a new small API. Keep the module roots, workspace, router, and checks already in use when extending a project.

## Rules for the project

- Start a server project with `cmd/api` for process setup and `internal` for private packages as the API grows. Put routing and request validation at the HTTP boundary. Keep domain operations and storage behind the owning package's interface. Introduce small interfaces at their consumers when a real boundary needs one.
- Pass `context.Context` through cancellable calls and operations that perform I/O. Handle errors explicitly and map them to HTTP at the handler. Keep configuration and secret values external to source. Document the API owner, response and error shapes, and local connection for a separate frontend.
- Keep dependencies reproducible with `go.mod` and `go.sum` when present. Record the minimum compatible Go version in `go.mod`, and use a selected supported Go release in CI. Exercise changed API behavior and include isolated integration coverage when the API adds a database or another external dependency.

See [Go's server layout](https://go.dev/doc/modules/layout#server-project), [toolchain semantics](https://go.dev/doc/toolchain), and [Go code review guidance](https://go.dev/wiki/CodeReviewComments).

## Checks and setup by mode

For the rules-only mode, record commands that exist and those still planned. The default CI checks are `gofmt -l` on project-owned Go files, `go vet ./...`, and `go test ./...`. Fail the formatting gate if `gofmt -l` prints any filenames or exits with an error. Run the checks from the relevant module roots. Use `testing` for unit tests and [`httptest`](https://pkg.go.dev/net/http/httptest) for the HTTP boundary. Add a race check for concurrency-sensitive changes when the platform and toolchain support it. [Go commands](https://pkg.go.dev/cmd/go) · [Race detector requirements](https://go.dev/doc/articles/race_detector#Requirements)

For config mode, initialize `go.mod` with the chosen module path and Go version if needed. Adopt existing CI when present. Connect formatting, vet, and tests once project packages exist. The standard toolchain needs no extra linter config for these checks.

For app mode:

1. Place the entry point in `cmd/api`. Use `http.NewServeMux` with a `GET /api/health` handler, and return HTTP 200 with `Content-Type: application/json` and `{ "status": "ok" }`. The method-qualified [route pattern](https://go.dev/blog/routing-enhancements) requires Go 1.22 or newer. Keep router setup callable by a test. Bind the sample server to `127.0.0.1:8000` or the loopback address and port agreed with the frontend. Set an explicit read-header timeout.
2. Add an `httptest` smoke test for the route and run formatting, vet, and tests. Check startup with `go run ./cmd/api` and a loopback request to the route. When an app includes a separate frontend, also check the frontend through its local API client. Shut down the local test process afterward.
