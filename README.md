# Compute Starter Kit Go Object Store

Fastly Object Store is a globally consistent key-value storage accessible across the Fastly Network. This makes it possible for your Compute@Edge application to read and write from Object-stores. The application shows how to use Fastly Object Store within a Go Compute@Edge application.

**For more details about other starter kits for Compute@Edge, see the [Fastly developer hub](https://developer.fastly.com/solutions/starters)**

## Deploying the project to Fastly

Note that Fastly Services have to have unique names within a Fastly Account.

To create and deploy to a new Fastly Service run the command and follow the instructions:

```shell
fastly compute publish
```

That is it, we now have a Fastly Service, a Fastly Object Store and have them linked together!

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
