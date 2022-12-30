# Instalação
Por padrão esse _chart_ vai ser instalado no _namespace default_. Para altear esse comportamento basta modificar a linha abaixo no arquivo `values.yaml`:

```yaml
# values.yaml

sealed-secrets:
  namespaceOverride: default
```

Para realizar a instalação basta executar:

```bash
[user@host ~]$ helm install ns-default-seald-secret sealed-secrets

NAME: ns-default-seald-secret
LAST DEPLOYED: Thu Dec 29 22:08:45 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```
Podemos verificar a instalação com o comando:

```bash
[user@host ~]$ helm list

NAME                 	NAMESPACE	REVISION	UPDATED                             	STATUS  	CHART        	APP VERSION
ns-default-seald-secret	default  	1       	2022-12-29 22:08:45.757755 -0300 -03	deployed	secrets-0.0.1	0.0.1
```

Por comodidade e simplicidade as dependências estão armazenadas junto com o chart _(não estava afim de ter que fazer um add no repo, depois um build pra depois fazer um install)_


# Gerando Secret

```bash
[user@host ~]$ NS=default
[user@host ~]$ oc create secret generic test-sec \
  --dry-run=client \
  --from-literal foo=bar -n $NS -o yaml | kubeseal --controller-namespace=$NS | oc apply -f -
```

# Certs

Podemos fazer o download dos certificados utilizados para gerar os secrets, basta executar o comando abaixo:


```bash
[user@host ~]$ kubeseal --controller-namespace=default -v10 --fetch-cert   
```

# Desinstalar 

Para desinstalar o _chart_ basta executar o comando `helm uninstal`:

```bash
[user@host ~]$ helm uninstall ns-default-seald-secret
release "ns-default-seald-secret" uninstalled
```
# Observações

Se por algum motivo o nome do _sealed secret controller_ for aterado no arquivo `values.yaml`, como podemos ver abaixo:

```yaml
sealed-secrets:
    fullnameOverride: se-sec-ctrl
```
Sempre que executarmos o comando `kubeseal` vamos precisar informar o nome do _controller_ usando a _flag_ `--controller-name`:

```bash
kubeseal --controller-name=se-sec-ctrl --controller-namespace=default --fetch-cert
```
