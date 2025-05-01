# [Static OIDC Issuer](https://github.com/therootcompany/sso-dev/)

<small><a href="https://github.com/therootcompany/sso-dev/">github.com/therootcompany/sso-dev</a></small>

A real, working OpenID Connect Configuration for Development \
(host statically on GitHub Pages, or wherever)

# Usage

Add any of these issuers to your web app's OpenID issuer whitelist:

-   <https://sso-dev.therootcompany.com/> (primary, ecdsa)
-   <https://sso-dev.therootcompany.com/dev/> (same as primary, but using subpath)
-   <https://sso-dev.therootcompany.com/demo/> (a different set of keys)
-   <https://sso-dev.therootcompany.com/ec/> (both ecdsa keys)
-   <https://sso-dev.therootcompany.com/rsa/> (both rsa keys)

Then sign a token (with the corresponding key) and run with it:

```sh
b_auth_time="$(date '+%s')"
b_standard_claims='{
    "amr": ["pwd"],
    "aud": "https://beta.therootcompany.com",
    "auth_time": '"${b_auth_time}"',
    "email": "me@example.com",
    "email_verified": false,
    "iss": "https://sso-dev.therootcompany.com",
    "locale": "en-US",
    "sub": "xxxxxxxxxxxx",
    "zoneinfo": "America/Denver"
}'

keypairs sign --exp 1h ./key.ec.jwk.json \
    "${b_standard_claims}" \
    > token.jwt \
    2> sig.jws

curl https://example.com/api/profile \
    -H "Authorization: Bearer $(cat ./token.jwt)"
```

## Directory Structure

From the root of <https://sso-dev.therootcompany.com> \
(also <https://therootcompany.github.io/sso-dev/>)

<pre><code>
.
├── <a href="./key.ec.jwk.json">key.ec.jwk.json</a>
├── <a href="./key.rsa.jwk.json">key.rsa.jwk.json</a>
├── .well-known/
│   ├── <a href="./.well-known/jwks.json">jwks.json</a>
│   └── <a href="./.well-known/openid-configuration">openid-configuration</a>
│
├── demo/
│   ├── <a href="./demo/key.ec.jwk.json">key.ec.jwk.json</a>
│   ├── <a href="./demo/key.rsa.jwk.json">key.rsa.jwk.json</a>
│   │
│   └── .well-known/
│       ├── <a href="./demo/.well-known/jwks.json">jwks.json</a>
│       └── <a href="./demo/.well-known/openid-configuration">openid-configuration</a>
│
├── dev/
│   └── .well-known/
│       ├── jwks.json
│       └── openid-configuration
├── ec/
│   └── .well-known/
│       ├── jwks.json
│       └── openid-configuration
└── rsa/
    └── .well-known/
        ├── jwks.json
        └── openid-configuration
</code></pre>

# Make it Yours

If you'd like to have your own test domain:

0. DO NOT PUBLISH production keys
1. Set a CNAME record \
   `<your-org>.github.io` for `<subdomain>.<your-org>.com`
2. Set the [`./CNAME`](./CNAME) file to `<subdomain>.<your-org>.com`
3. Install [`keypairs`](https://webinstall.dev/keypairs) \
   (because it's easy and cross-platform)
    ```sh
    curl -sS https://webi.sh/keypairs | sh
    source ~/.config/envman/PATH.env
    ```
4. Replace the originals with your own keys \
   (uses `keypairs` in the script)
    ```sh
    rm -rf *.jwk.json ./ec/ ./rsa/ ./dev/ ./demo/
    ./bin/generate-keys https://sso-dev.therootcompany.com
    ```
5. Host on GitHub Pages (or wherever) \
   Settings => Pages => Branch: main

# LICENSE

Source: <https://github.com/therootcompany/sso-dev>

[Public Domain](https://therootcompany.com/blog/how-to-release-software-into-the-public-domain/) via [CC0-1.0](https://creativecommons.org/publicdomain/zero/1.0/)
