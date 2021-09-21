<p align="center"><a href="https://kubernetes.io/pt-br/" target="_blank"><img src="https://miro.medium.com/max/1200/1*fe7IqPpDckaVjjysqq8jhg.png" width="400"></a></p>

# Requerimentos necessário
- Kubectl;
- Minikube;
- Docker.

# Comandos para executar e explicação
Inicialmente vamos executar ```minikube start``` para iniciar configurações padrões do *minikube* (ferramenta para virtualizar os *clusters* dentro da máquina local). E depois ```minikube dashboard``` para termos um *dashboard* de controle para verificar o que está acontecendo dentro do *cluster* também é possível verificar tudo por linha de comando.

Partindo para as ferramentas do *Kubernetes* vamos começar com o comando ```kubectl apply -f demo-kubernetes-deploment.yaml```. Com isso vamos ter um *deployment* de apenas um *POD* que está configurado na chave *replicas*.

Após criarmos um *deployment* ainda não vamos conseguir acessar o *cluster* pois não o expomos.

Para isso vamos rodar o seguinte comando ```kubectl expose deployment demo-kubernetes-deployment --type=LoadBalancer --port=8000```. Vamos usar o valor *LoadBalancer* no parâmetro para depois escalarmos nossos *PODs* e termos o balanceamento entre eles.

Com isso ainda não será expostos um acesso externo, por padrão localmente rodando o *cluster* não  temos suporte para tipo *LoadBalancer*, porém com o *minikube* podemos executar o comando ```minikube tunnel```, este comando irá pegar todos os *services* exposto e vai redirecionar os *IPs* do *cluster* para um *ip* externo acessível.

Se executarmos ```kubectl describe demo-kubernetes``` vamos perceber que terá uma chave chamada *Endpoints* essa chave representa o *IP* de cada *PODs* construído dentro do *cluster*. E isso também é o host que vamos usar para rodar a aplicação do Laravel dentro do *POD*.

Então, se entramos no *POD* com o comando ```kubectl exec --stdin --tty nome-do-meu-pod -- /bin/bash``` e executarmos ```php artisan serve --host=host-do-endpoint-que-vimos-anteriormente```. Agora com a aplicação rodando, nós podemos pegar o *IP* externo do nosso serviço exposto e acessar no *browser* e será possível ver a aplicação rodando.

# Para acessar no browser
Execute o comando ```kubectl get services``` com o ```minikube tunnel``` rodando e iremos usar o *EXTERNAL-IP* para acessar o *browser* na porta 8000. Com isso, um redirecionamento para dentro do *cluster* irá ser feito e lá o balanceamento entreos *PODs* vai ocorrer.

# Escalar
Para escalarmos, vamos precisar rodar o seguinte comando ```kubectl scale deployments/demo-kubernetes- --replicas=2``` (fique a vontade de mudar a quantidade de réplica). Isso vai criar mais *PODs* e como já definimos o nosso serviço como *LoadBalancer* o balanceamento entre os *PODs* já irá ocorrer entre eles. Porém, vamos ter que entrar novamente no novo *POD* e rodar o servidor com o comando do Laravel com o host do novo *POD*.

# Env
Em cada *POD* há um arquivo *.env* altere o valor (reinicie o servidor) de cada um e veja o balancemanto acontecer.

# Conclusão
Isso é apenas um estudo inicial sobre *Kubernetes* ainda falta fazer algumas tarefas como:
- [ ] Rodar o servidor automaticamente ao criar o *POD*;

Caso perceba alguma explicação errada, fique a vontade para enviar uma *PR*. Caso queira aprimorar em geral o que foi feito até agora, fique a vontate para mudar :smile:.