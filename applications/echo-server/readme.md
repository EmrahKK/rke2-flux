/kv/tams-ui/client-secret

vault write auth/kubernetes/config token_reviewer_jwt="$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" kubernetes_host="https://$KUBERNETES_PORT_443_TCP_ADDR:443" kubernetes_ca_cert=@/var/run/secrets/kubernetes.io/serviceaccount/ca.crt


vault write auth/kubernetes/role/tams-ui-read 
bound_service_account_names=echo-server 
bound_service_account_namespaces=echo-server 
policies=tams-ui-read 
ttl=72h


vault write auth/kubernetes/role/webapp bound_service_account_names=echo-vault-auth bound_service_account_namespaces=echo-server policies=svc-policy ttl=72h



kubectl create serviceaccount echo-vault-auth -n echo-server

Sep 16, 2022 12:15 AM
Thu Sep 15 21:18:12 UTC 2022

----------------------------------------------------

# create internal kv store
vault secrets enable -path=internal kv-v2

# create internal/app/echo-server secret
vault kv put internal/app/echo-server db_username="ech-server" db_password="12345678"	 

# create read policy for the secret
vault policy write echo-server - <<EOF
path "internal/app/echo-server" {
  capabilities = ["read"]
}
EOF

# create role to bind the service account and policy

vault write auth/kubernetes/role/echo-server bound_service_account_names=echo-server bound_service_account_namespaces=echo-server policies=echo-server ttl=24h
