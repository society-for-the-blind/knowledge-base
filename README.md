## How to save SSH private/public keys to Azure key vault?

```text
az keyvault secret set --vault-name 'myvault' --name 'secret-name' --file '~/.ssh/id_rsa'
```

## How to retrieve SSH private/public keys from Azure key vault, and use them on a new/another Linux system?

```bash
# Retrieve the private key and save it into a file
az keyvault secret show --vault-name "sftb-vault" --name "ssh-private-key-1" \
| echo -e $(jq '.value') \
| tr -d '"'              \
| sed '$d'               \
> id_rsa

# Retrieve the public key and save it into a file
az keyvault secret show --vault-name "sftb-vault" --name "ssh-public-key-1" \
| echo -e $(jq '.value') \
| tr -d '"'              \
| sed '$d'               \
> id_rsa.pub

# From https://askubuntu.com/questions/786998/how-to-use-existing-ssh-key-on-my-newly-installed-ubuntu

# Copy  keys to  SSH directory (e.g.,  `~/.ssh`) (in
# case they were retrieved in another directory)
cp /path/id_rsa /path/id_rsa.pub ~/.ssh

# Set permissions
sudo chmod 600 ~/.ssh/id_rsa
sudo chmod 600 ~/.ssh/id_rsa.pub

# start the ssh-agent in the background
eval $(ssh-agent -s)

# make ssh agent to actually use copied key
ssh-add ~/.ssh/id_rsa
```

If `ssh-add` doesn't add doesn't permanently add the keys, try suggestions from [here](https://stackoverflow.com/questions/3466626/how-to-permanently-add-a-private-key-with-ssh-add-on-ubuntu).
