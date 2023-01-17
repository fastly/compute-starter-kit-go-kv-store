# Object Store Go Template

Fastly Object Store is a globally consistent key-value storage accessible across the Fastly Network. This makes it possible for your Compute@Edge application to read and write from Object-stores. The application shows how to use Fastly Object Store within a Go Compute@Edge application.

**For more details about other starter kits for Compute@Edge, see the [Fastly developer hub](https://developer.fastly.com/solutions/starters)**

## Getting Started

### Installing the Fastly CLI

The [Fastly CLI](https://developer.fastly.com/learning/tools/cli/) is available for multiple operating systems.

- **MacOS**: Install from Homebrew:

    ```
    brew install fastly/tap/fastly
    ```

- **Windows**: [Visit the GitHub repository](https://github.com/fastly/cli/releases/latest) to download the prebuilt binary for your architecture.
- **Linux**: Packages are available for many distributions, along with prebuilt binaries. [Visit the GitHub repository](https://github.com/fastly/cli/releases/latest) to download the package for your distribution.

### Initialise a new application

Create a new Fastly Compute@Edge project using this template:

```shell
fastly compute init --from https://github.com/fastly/object-store-go-template
```

### Run the application locally

To run the application locally we only need to run one command:

```shell
fastly compute serve
```

The application should now be running on <http://127.0.0.1:7676>

The `fastly.toml` file has a section named `[local_server]` which contains the configuration for Object Store when running locally.

## Deploying the project to Fastly

### Create an Object Store

Object Stores have to have unique names within a Fastly Account.

To create an Object Store we run the following command, replacing `<UNIQUE_STORE_NAME_GOES_HERE>` with the name for the new Object Store:

```shell
fastly objectstore create --name=<UNIQUE_STORE_NAME_GOES_HERE>
```

Make a note of the Object Store's ID, the ID can be found by listing the Object Stores:
```shell
fastly objectstore list | grep -A 3 '<UNIQUE_STORE_NAME_GOES_HERE>'
```

### Deploy Application to Fastly Service

Note that Fastly Service have to have unique names within a Fastly Account.

To create and deploy to a new Fastly Service run the command and following the instructions:

```shell
fastly compute publish
```

### Link Object Store with Fastly Service

To use an Object Store within a Fastly Service, we need to link the two together. At this point, we can give the linked Object Store a more generic name for use within the Fastly Service.

First we need to create a new version of our Fastly Service:

```shell
fastly service-version clone --version=latest
```

The command should respond with a message such as:
```shell
SUCCESS: Cloned service ovHQo6jnDPzsZW7hbAHKv3 version 1 to version 2
```

Make note of the Service ID and the new version. E.G. The message above has the Service ID `ovHQo6jnDPzsZW7hbAHKv3` and the new version is `2`.

Now we can Link the Object Store with the Fastly Service by running this cURL command:

```shell
curl -i  -X POST "https://api.fastly.com/service/<$FASTLY_SERVICE_ID>/version/<$FASTLY_SERVICE_VERSION>/resource" -H "Fastly-Key: $(fastly profile token)" -H "Content-Type: application/x-www-form-urlencoded" -H "Accept: application/json" -d "name=my-store&resource_id=<$FASTLY_OBJECT_STORE_ID>"
```

The final step is to active the new version:
```shell
fastly service-version activate --version=2
```

That is it, we now can use the Object Store within the Fastly Service!

You can view real-time logs for the Fastly Service by running:
```shell
fastly log-tail
```

## Adding entries to the Object Store

It is possible to add key-value pairs to an Object Store using the Fastly CLI like so:
```shell
fastly objectstore insert --id=$FASTLY_OBJECT_STORE_ID --k=$KEY --v=$VALUE
```

For example, here is how you could add to the Object Store named `my-store` a key named `readme` whose value is the contents of `README.md`:
```shell
fastly objectstore get --id="$(fastly objectstore list | grep -A 3 'my-store' | tail -n 1 | grep -o '[a-z0-9]*')" --k="readme" --v="$(cat README.md)"
```

## Security issues

Please see our [SECURITY.md](SECURITY.md) for guidance on reporting security-related issues.