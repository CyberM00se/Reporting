# Socat

Socat is helpful to route traffic or change protocols.

### Host

```
sudo socat UDP4-LISTEN:50001,fork TCP4:localhost:5222
```

### Pivot

```
./socatx64.bin TCP4-listen:5222,fork UDP4:10.0.6.52:50001
```

