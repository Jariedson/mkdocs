# Dicas Windows 

Documentação com dicas sobre o Windows

##### Adicionar rota permanente no Windows


No exemplo a seguir, será criada uma rota para a rede 172.20.254.0/24 através do gateway 172.20.254.254

Execute o CMD como administrador e digite o seguinte comando


```
 route add 172.20.254.0 mask 255.255.255.0 172.20.254.254 -p

```

O parâmetro `-p` foi usado para que a rota seja permanente, que não seja apagada quando o sistema for desligado.

##### Modicificar o arquivo hosts


Abra o bloco de notas como administrador e edite o serguinte arquivo

` C:\Windows\System32\drivers\etc\hosts `

O arquivo deve ter a seguinte sintaxe


``` 

IP					Nome do host 

```
