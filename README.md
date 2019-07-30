# DHCP
O DHCP, Dynamic Host Configuration Protocol (Protocolo de configuração dinâmica de host), é um protocolo de serviço TCP/IP que oferece configuração dinâmica de terminais, com concessão de endereços IP de host, Máscara de sub-rede, Default Gateway (Gateway Padrão), Número IP de um ou mais servidores DNS, Número IP de um ou mais servidores WINS e Sufixos de pesquisa do DNS.

## Instalação
Toda a instalação é feita como **root**

### Debian
Instale o dhcp server com o comando:

`apt-get install isc-dhcp-server`

Edite o arquivo **/etc/default/isc-dhcp-server colocando** a interface de rede na qual o dhcp server vai prestar o serviço alterando a linha:

```
INTERFACESV4="" para INTERFACESV4="interface" (onde a interface pode ser eth0, eth1, ...) caso seja mais que uma separe-as por espaço
ou INTERFACESV6="" se for IPv6
```

Após editar o arquivo, salve-o e feche-o.

Para o serviço funcionar corretamente edite o arquivo **/etc/dhcp/dhcpd.conf** ou **dhcpd6.conf** se for IPv6, abaixo segue um exemplo:

```
default-lease-time 600;
max-lease-time 7200;
option subnet-mask 255.255.255.0;
option broadcast-address 192.168.2.255;
option routers 192.168.2.254;
option domain-name-servers 192.168.2.1, 8.8.8.8;

subnet 192.168.2.0 netmask 255.255.255.0 {
  range 192.168.2.2 192.168.2.20;
}
```

Após editar o arquivo, salve-o e feche-o.

Inicie o serviço com o comando:

`service isc-dhcp-server start`

Caso não tenha errado na sintaxe o serviço iniciará normalmente ou apresentará uma mensagem de erro.

Arquivo onde pode ser localizado qual **IP** foi atribuído a qual MAC Address:

`/var/lib/dhcp/dhcpd.leases`

### CentOS
Instale o dhcp server com o comando:

`yum -y install dhcp`

Para o serviço funcionar corretamente edite o arquivo **/etc/dhcp/dhcpd.conf** ou **dhcpd6.conf** se for IPv6, abaixo segue um exemplo:

```
default-lease-time 600;
max-lease-time 7200;
option subnet-mask 255.255.255.0;
option broadcast-address 192.168.2.255;
option routers 192.168.2.254;
option domain-name-servers 192.168.2.1, 8.8.8.8;

subnet 192.168.2.0 netmask 255.255.255.0 {
  range 192.168.2.2 192.168.2.20;
}
```

Após editar o arquivo, salve-o e feche-o.

Faça o teste de configuração com o comando:

`service dhcpd configtest`
Se tudo estiver OK inicie o serviço com o comando:

`service dhcpd start`
Para fazer que o serviço seja carregado no boot use o comando:

`  systemctl enable dhcpd.service`
Arquivo onde pode ser localizado qual **IP** foi atribuído a qual MAC Address:

`/var/lib/dhcpd/dhcpd.leases`

## Bloqueio por MAC Address

Edite o arquivo **/etc/dhcp/dhcpd.conf** ou **dhcpd6.conf** se for IPv6 e seguindo o exemplo acima, e antes da declaração de subnet coloque a linha:

` deny unknown-clients;`
Isto impedirá que qualquer MAC pegue um endereço IP

Caso queira liberar o atribuir um IP Fixo a um MAC, sabendo o MAC edite o arquivo **/etc/dhcp/dhcpd.conf** ou **dhcpd6.conf** se for IPv6 e abaixo da finalização da declaração do range coloque as linhas:

```
host maquina01 {
        hardware ethernet XX:XX:XX:XX:XX:XX; (Onde XX:XX:XX:XX:XX:XX é o MAC Address)
        fixed-address 192.168.2.2;
}
```

Lembre-se que após qualquer alteração estando como root reinicie o serviço com o comando:

## Debian

`service isc-dhcp-server restart`

## CentOS

` service dhcpd restart`