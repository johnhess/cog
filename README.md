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

When an envoy url containing `ohttp=1` is used, the request is relayed over OHTTP.  At present the relay and destination are hardcoded/expected to be `ohttp-relay.jthess.com` and `ohttp-gateway.jthess.com`.