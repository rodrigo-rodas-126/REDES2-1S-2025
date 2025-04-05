# Comandos utilizados - BGP

## MSW1
```shell
router bgp 65001
neighbor 10.0.50.2 remote-as 65002
neighbor 10.0.60.2 remote-as 65003
network 10.0.10.0 mask 255.255.255.252
```

```shell
router ospf 1
redistribute bgp 65001 subnets
```

```shell
router bgp 65001
redistribute ospf 1
```

## MSW2

```shell
router bgp 65002
neighbor 10.0.50.1 remote-as 65001
neighbor 10.0.70.2 remote-as 65003
network 10.0.20.0 mask 255.255.255.252
```

```shell
router ospf 2
redistribute bgp 65002 subnets
```

```shell
router bgp 65002
redistribute ospf 2
```

## MSW3

```shell
router bgp 65003
neighbor 10.0.60.1 remote-as 65001
neighbor 10.0.70.1 remote-as 65002
network 10.0.30.0 mask 255.255.255.252
```

```shell
router ospf 3
redistribute bgp 65003 subnets
```

```shell
router bgp 65003
redistribute ospf 3
```

# Otros comandos

```shell
# Ver estado de vecinos BGP
show ip bgp summary
# Ver tabla BGP completa
show ip bgp
# Ver información específica de un vecino
show ip bgp neighbors [IP_VECINO]
# Forzar refresco de rutas
clear ip bgp *
```