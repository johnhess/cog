# `cog`, a Client for OHTTP by Guardian Project

This is a client for making Oblivious HTTP requests as described in [RFC 9458](https://www.rfc-editor.org/rfc/rfc9458.html).  It's also a _lot_ more, since it's based on Chromium's [Cronet](https://chromium.googlesource.com/chromium/src/+/refs/heads/main/components/cronet) networking stack (More precisely, it's based on [Greatfire's Envoy](https://github.com/greatfire/envoy)).

This means you can use this Oblivious HTTP Client implementation in any of the places you might use Cronet: in Chromium, mobile apps, on the desktop, and more.

## How to use this

Like Envoy itself, you can optionally configure an `envoy_url` when initializing Cronet.  You can avail yourself of the usual Envoy proxies requests, and you can also specify an `ohttp` proxy.

```
Cronet_EngineParams_envoy_url_set(
    engine_params,
    "envoy://?url=https%3A%2F%2Fohttp-relay.jthess.com"  // Relay URL
    "&ohttp=1"                                           // Use OHTTP for this relay
    "ohttp-config=<base64 encoded ohttp key config>"     // Config per section 3 of RFC
)
```

A working example of this setup can be found in `$CHROMIUM_SRC_ROOT/components/cronet/native/sample/main.cc` once you've applied this patch.

## Patching and Building

Assuming `$CHROMIUM_SRC_ROOT`, `cog` and `envoy` are in sibling folders and you've already set up the prerequisites for `envoy`, you can build and run this using:

```
# Apply the patch
cd envoy
./native/build_cronet.sh debug
cd $CHROMIUM_SRC_ROOT
git apply ../cog/ohttp.patch

# Then build whichever Chromium targets interest you:
autoninja -C out/Cronet-Desktop cronet cronet_sample ohttp_test
```

The example `main.cc` mentioned above is built as the target `cronet_sample` and can be run as

```
./out/Cronet-Desktop/cronet_sample <Target URL>
``` 

You can also run `ohttp-gp`'s tests in the Chromium build system.  Because these exercise the Chromium-provided BoringSSL implementation, this provides assurances beyond running these same tests in their ordinary context.

```
./out/Cronet-Desktop/ohttp_test
``` 

### Updating this patch

This diff consists of two main portions.

1. An Envoy-like patch of the actual Chromium/Cronet source.  This injects proxying logic in the networking stack transparently to the Cronet user.  Like Envoy's patch, this is relatively brittle and requires us to do the work of maintaining a fork.
2. The actual OHTTP library implementation.  This is patched into `//third_party/ohttp` and copied verbatim from the `ohttp-gp` [repository](https://github.com/johnhess/ohttp-gp).

Updates to `ohttp-gp` should go through the upstream repository.  As a developer, you will find that modifying `ohttp-gp` and running its tests is far simpler than building chromium.  

To modify the non-`ohttp-gp` portion of the patch, modify the cog-patched chromium code, then diff your branch against a branch with just envoy's patch:

```
cd $CHROMIUM_SRC_ROOT
git diff <my-envoy-patched-branch> <my-working-branch> > /path/to/this/repository/ohttp.patch
```
