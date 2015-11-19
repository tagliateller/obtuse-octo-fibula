
https://github.com/kubernetes/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md


```console
git clone https://github.com/kubernetes/kubernetes
cd kubernetes/docs/getting-started-guides/coreos/azure/
```

```console
npm install
```

Bedingt, möglicherweise ist das nicht das Problem:

Azure-cli sollte 0.9.12 sein, sonst:

```console
sudo npm update -g azure-cli
```

Aber:

```console
azure config mode asm
```

