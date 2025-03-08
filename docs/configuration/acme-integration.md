
# ACME Integration (RFC8555)

This document explains how to configure OvenMediaEngine to use the ACME protocol (RFC8555) for automated certificate management with Let's Encrypt or other ACME providers.

## Configuration in Server.xml

To enable ACME certificate management in OvenMediaEngine, you need to add an `ACME` section to your `Server.xml` configuration file under the `Modules` section:

```xml
<Modules>
    <!-- Other module configurations -->
    
    <ACME>
        <Enable>true</Enable>
        <Directory>/opt/ovenmediaengine/bin/acme</Directory>
        <Email>your-email@example.com</Email>
        <AgreeTOS>true</AgreeTOS>
        <ChallengeType>http-01</ChallengeType>
        <!-- Optional: Specify renewal period in days (default: 30) -->
        <RenewalDays>30</RenewalDays>
    </ACME>
</Modules>
```

Then, in your `Host` configuration, reference the ACME certificate:

```xml
<Host>
    <Names>
        <Name>example.com</Name>
    </Names>
    <TLS>
        <UseACME>true</UseACME>
    </TLS>
</Host>
```

## Configuration Parameters

- `Enable`: Set to `true` to enable ACME certificate management
- `Directory`: Path to store ACME-related files
- `Email`: Your email address for Let's Encrypt notifications
- `AgreeTOS`: You must agree to the Terms of Service (set to `true`)
- `ChallengeType`: The challenge type to use (currently only `http-01` is supported)
- `RenewalDays`: Number of days before expiration to attempt renewal

## Usage with Docker

When running OvenMediaEngine in Docker, make sure to mount a volume for the ACME directory to persist certificates:

```bash
docker run --name ome -d -e OME_HOST_IP=Your.HOST.IP.Address \
-p 1935:1935 -p 9999:9999/udp -p 9000:9000 -p 3333:3333 -p 3478:3478 -p 10000-10009:10000-10009/udp \
-p 80:80 -p 443:443 \
-v ome-acme:/opt/ovenmediaengine/bin/acme \
-v ome-origin-conf:/opt/ovenmediaengine/bin/origin_conf \
-v ome-edge-conf:/opt/ovenmediaengine/bin/edge_conf \
airensoft/ovenmediaengine:latest
```

Note that port 80 must be exposed for the HTTP-01 challenge to work.
