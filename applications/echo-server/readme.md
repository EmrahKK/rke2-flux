/kv/tams-ui/client-secret

vault write auth/kubernetes/config token_reviewer_jwt="$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" kubernetes_host="https://$KUBERNETES_PORT_443_TCP_ADDR:443" kubernetes_ca_cert=@/var/run/secrets/kubernetes.io/serviceaccount/ca.crt


vault write auth/kubernetes/role/tams-ui-read 
bound_service_account_names=echo-server 
bound_service_account_namespaces=echo-server 
policies=tams-ui-read 
ttl=72h