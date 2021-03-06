# Kube OpenAPI Integration Tests

## Running the integration tests

Within the current directory:

```bash
$ go test -v .
```

## Generating the golden Swagger definition file and API rule violation report

First, run the generator to create `openapi_generated.go` file which specifies
the `OpenAPIDefinition` for each type, and generate the golden API rule
violation report file . Note that if you do not pass a report
filename (`./testdata/golden.report` in the command below) to let the generator
to print API rule violations to the file, the generator will return error to stderr
on API rule violations.

```bash
$ go run ../../cmd/openapi-gen/openapi-gen.go -i "k8s.io/kube-openapi/test/integration/testdata/listtype,k8s.io/kube-openapi/test/integration/testdata/dummytype,k8s.io/kube-openapi/test/integration/testdata/uniontype" -o pkg -p generated -O openapi_generated -r ./testdata/golden.report
```
The generated file `pkg/generated/openapi_generated.go` should have been created.

Next, run the OpenAPI builder to create the Swagger file which includes
the definitions. The output file named `golden.json` will be output in
the current directory.

```bash
$ go run builder/main.go testdata/golden.json
```

After the golden spec is generated, please clean up the generated file
`pkg/generated/openapi_generated.go` before you commit. It's an intermediate product that doesn't need to be updated in kube-openapi repository. The checked-in file is kept minimum to make sure that `test/integration/builder` compiles. Please run:

```base
$ git checkout pkg/generated/openapi_generated.go
```

to discard any local change.
