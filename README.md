# envtpl

`envtpl` renders [Go templates] on the command line using environment variables.

It is directly inspired by the original [envtpl], a Python tool for rendering
[Jinja2] templates.

This port was motivated by the desire to add templating support for template-driven
configuration files that needed to be part of a base Docker image without also
requiring the installation of Python. For the same reason, I decided not to add
variable support to my previous template utility based on Node.js, [njx].

Despite the difference between `Jinja` and `Go` templates, an attempt was made
to match the command line syntax of the original `envtpl`.

The biggest obvious difference is that `Go` template variables represent a path within
a data context, so `envtpl` variables will need to be prepended with a leading `.` to
match the keys of the internal environment variable map object (see example).

## Usage

    envtpl [-o|--output outfile] [template]

* If `template` is not provided, `envtpl` reads from `stdin`
* If `outfile` is not provide, `envtpl` writes to `stdout`

## Example

`greeting.tpl`

    Hello {{.USER}}

Render the template (assume the value of `$USER` is 'mary')

    envtpl greeting.tpl  # writes "Hello mary" to stdout

    USER=bob envtpl greeting.tpl  # overrides "mary" and writes "Hello bob" to stdout

    echo "greetings {{.USER}}" | envtpl  # writes "greetings mary" to stdout

    envtpl < greeting.tpl > out.txt  # writes "Hello mary" to out.txt

    cat greeting.tpl | envtpl > out.txt  # writes "Hello mary" to out.txt

## Template Functions

In addition to the [standard set of template actions and functions][standard-templates]
that come with Go, `envtpl` also incorporates [sprig] for additional, commonly used functions.

For example:

    echo "Greetings, {{.USER | title}}" | envtpl  # writes "Greetings, Mary" to stdout

## Building an envtpl executable

The `build.sh` script can be used to build the `envtpl` executable. If you provide
the `alpine` argument, it will build a binary for Alpine Linux. This build script
is intended for Docker workflows; it does not require Go support, only Docker.

## Building an envtpl Docker image

The provided `Dockerfile` can be used to create an image for `envtpl`. It will
copy `envtpl` from the repo directory that was built by `build.sh`.
Because the `Dockerfile` is based on the `alpine` image, be sure to first build
an executable for Alpine with the `alpine` option (`./build.sh alpine`).

## Similar Tools

As mentioned above, this tool was inspired by the original [envtpl] project and
motivated to provide something similar without adding a Python dependency to
Docker base images.

A search for similar Go-based tools turns up the following:

 * [mattrobenolt/envtpl]
 * [arschles/envtpl]

I haven't spent any time evaluating either yet. However, [mattrobenolt/envtpl] looks elegantly simple and [arschles/envtpl] offers tests, [glide] package management support and more template functionality using [sprig].

Neither of these two packages appear to conform to the original `envtpl` command line syntax, which was one of my goals, although I don't think this is a big deal since all of these spin-off versions use an entirely different template syntax anyway. However, at first glance at least, this variant does offer more input/output options modeled after the original.

I'm inspired by [arschles/envtpl] to add ~~[sprig] support for extended template functions~~, potentially [glide] support, and definitely tests. This version now has [sprig] template support also (credit to [arschles/envtpl]).

## License

[MIT](https://raw.githubusercontent.com/subfuzion/envtpl/master/LICENSE)


[arschles/envtpl]:     https://github.com/arschles/envtpl
[envtpl]:              https://github.com/andreasjansson/envtpl
[glide]:               https://github.com/Masterminds/glide
[Go templates]:        https://golang.org/pkg/text/template/
[Jinja2]:              http://jinja.pocoo.org/docs/dev/
[mattrobenolt/envtpl]: https://github.com/mattrobenolt/envtpl
[njx]:                 https://github.com/subfuzion/njx
[sprig]:               https://github.com/Masterminds/sprig
[standard-templates]:  https://golang.org/pkg/text/template/
