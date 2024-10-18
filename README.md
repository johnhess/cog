# Client for OHTTP by Guardian Project

Like [Envoy](https://github.com/greatfire/envoy) itself, this is a diff that can be applied to Chromium's constituent Cronet.

Assuming `$CHROMIUM_SRC_ROOT`, `cog` and `envoy` are in sibling folders and you've already set up the prerequisites for `envoy`, you can build and run this using:

```
cd envoy
./native/build_cronet.sh debug
cd $CHROMIUM_SRC_ROOT
git apply ../cog/ohttp.patch
autoninja -C out/Cronet-Desktop cronet cronet_sample cronet_sample_test && ./out/Cronet-Desktop/cronet_sample https://ohttp-gateway.jthess.com
```

## How this works

When an envoy url containing `ohttp=1` is used, the request is relayed over OHTTP.  At present the relay and destination are hardcoded/expected to be `ohttp-relay.jthess.com` and `ohttp-gateway.jthess.com`.

## Updating this patch

The easiest way to develop this patch is to modify the envoy-patched chromium code, then diff your branch against the envoy-patched version.

```
git diff my-envoy-patched-branch my-working-branch > ../ohttp.patch
```

## Work in progress

This is currently a proof of concept and is being refined towards a pull request against `envoy`.  Some things that will happen in that process:

* Our `ohttp` implementation will be factored into a library and moved to `third_party` in keeping with the Google/Chrome norms.  The library will be distributed and licensed separately.
* We'll document a manner for configuring OHTTP including
    * Specifying relay & gateway
    * Getting and parsing `ohttp-keys`
* We'll debug and compatibility test this `ohttp` implementation against other relays and gateways.

## Other resources

Our relay, [`pog`](https://github.com/johnhess/pog).  And a forthcoming gateway based on the C++ library underway here.

Other implementations:

* Library:
    * https://github.com/chris-wood/ohttp-go (golang)
* Client
    * https://blog.cloudflare.com/building-privacy-into-internet-standards-and-how-to-make-your-app-more-private-today/ (golang example)
    * https://github.com/martinthomson/ohttp (rust)
    * https://github.com/cloudflare/app-relay-client-library (rust)
* Relays
    * https://github.com/cloudflare/privacy-gateway-relay (cloudflare worker)
* Gateways
    * https://github.com/martinthomson/ohttp (rust)
    * https://github.com/cloudflare/privacy-gateway-server-go (golang)