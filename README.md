# First Step is enabling the secret engine
`vault secrets enable gcp`

`vault write gcp/config credentials=@creds.json`

```
vault write gcp/roleset/my-token-roleset \        
    project="main-tokenizer-364009" \
    secret_type="access_token"  \
    token_scopes="https://www.googleapis.com/auth/cloud-platform" \
    bindings=@gcp-policy.hcl
```


# Second step is enabling the Authentication

`vault auth enable -path=jwt-demo jwt`

```
vault write auth/jwt-demo/config \
    oidc_discovery_url="https://token.actions.githubusercontent.com" \
    bound_issuer="https://token.actions.githubusercontent.com" \
    default_role="github-actions"
```


```
vault write auth/jwt-demo/role/github-actions \
    role_type="github-actions" \
    bound_claims_type="glob" \
    bound_claims='{"sub":"repo:Arminmsg/tf-vault-gcp:ref:refs/*"}' \
    bound_audiences="https://github.com/Arminmsg" \
    user_claim="workflow" \
    ttl=1h
```