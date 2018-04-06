
```
git clone https://github.com/starkandwayne/operator-workshop.git
cd operator-workshop/
```

```
sudo bin/install-tools
sudo bin/setup-lab-1
```

```
cd lab-1
. env-variables
bbl plan
time bbl up
eval "$(bbl print-env)"
bbl ssh --jumpbox
```
