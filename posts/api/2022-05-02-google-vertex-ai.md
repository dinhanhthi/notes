---
layout: post
title: "Google Vertex AI"
tags: [API & Services, MLOps, Google]
toc: true
notfull: true
icon: vertex.png
keywords: "vertex ai machine learning service google gcloud cli"
date: 2022-05-04
---

{% assign img-url = '/img/post/api/vertex' %}

## Good to know

- You should choose **the same location/region** for all services (google project, notebook instances,...)
- [Troubleshooting](https://cloud.google.com/vertex-ai/docs/general/troubleshooting).
- [Access Cloud Storage buckets](https://cloud.google.com/vertex-ai/docs/workbench/managed/cloud-storage).

## Notebooks (Workbench)

### Workbench notebook vs Colab

👉 Note: [Google Colab](/google-colab/).

|                    | Workbench notebook                | Colab                    |
| ------------------ | --------------------------------- | ------------------------ |
| Free               | No                                | Yes (but limit)          |
| Persistent storage | Yes                               | **No**                   |
| Easy to share      | No                                | Yes                      |
| Idle time          | No (user-managed) & Yes (Managed) | Yes (1h in free version) |



### "Managed notebook" vs "User-managed notebook"

👉 [Official doc](https://cloud.google.com/vertex-ai/docs/workbench/notebook-solution). Below are some notable points.

|                                                              | Managed notebook                                             | User-managed notebook                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------ |
| SSH                                                          | **[No](https://cloud.google.com/vertex-ai/docs/general/troubleshooting#unable_to_ssh_into_instance)** (but [we can](#ssh-managed) 😉) | Yes                                              |
| sudo access                                                  | **No**                                                       | Yes                                              |
| [Idle shutdown](https://cloud.google.com/vertex-ai/docs/workbench/managed/idle-shutdown) | Yes                                                          | **No** (==remember to shutdown when not using==) |
| Flexibility                                                  | No                                                           | Yes                                              |
| [Schedule notebook run](https://cloud.google.com/vertex-ai/docs/workbench/managed/schedule-managed-notebooks-run-quickstart) | Yes                                                          | **No**                                           |
| Connect with [Compute Engine](https://cloud.google.com/compute/docs) | **No**                                                       | Yes                                              |
| [Health status monitoring](https://cloud.google.com/vertex-ai/docs/workbench/user-managed/monitor-health) | No                                                           | Yes                                              |

### `gcloud` CLI

👉 [Official references](https://cloud.google.com/sdk/gcloud/reference).

Start instance,

```bash
gcloud compute instances start thi-managed-notebook --zone=europe-west1-d
```

Stop instance,

```bash
gcloud compute instances stop thi-managed-notebook --zone=europe-west1-d
```

### Sync with Github

::: info

- If you use Terminal on the notebook, you cannot use git as normal, you have to use Personal Access Token to log in.
- If you use SSH to connect to the container running your notebook, you basically have all rights to do with git like on your local machine.

:::

### SSH to access JupyterLab (User-managed notebook)

::: warning
You have to use **User-managed notebook**! **Managed notebook** doesn't allow you to use SSH (officially).  If you wanna connect via SSH for **managed notebook**, read [next  section](#ssh-managed).
:::

First, connect using `gcloud` 👉 Note: [Google Cloud CLI](/google-cloud-cli/).

👉 Note: [SSH](/ssh/)

::: hsbox An overview interface of Workbench
![An overview of "user-managed notebook"]({{img-url}}/vertex_workbench.jpg)
:::

::: hsbox Connect via `gcloud` command + SSH port forwarding

👉 [Official doc](https://cloud.google.com/vertex-ai/docs/workbench/user-managed/ssh-access).

```bash
gcloud compute ssh --project <project-id> --zone <zone> <instance-name> -- -L 8081:localhost:8080
```
:::

- You can find all information of `<thing>` by clicking the notebook name in Workbench. ==`8081` is the port on your machine== and `8080` is the port on vertex.
- For `<instance-name>`, you can also use instance id (which can be found in *Compute Engine* > *VM instances*)

::: hsbox Connect via `ssh` (and also on VScode)
You can follow [the official instructions](https://cloud.google.com/compute/docs/instances/connecting-advanced#thirdpartytools). For me, they're complicated. I use another way.

Make sur you've created a ssh keys on your local machine, eg. `/Users/thi/.ssh/id_rsa.ideta.pub` is mine.

```bash
# Show the public keys
cat /Users/thi/.ssh/id_rsa.ideta.pub
# Then copy it
```

On the vertex notabook instance (you can use `gcloud` method to access or just open the notebook on browser and then open Terminal).

```bash
# Make sure you are "jupyter" user
whoami # returns "jupyter"
# If not
su - jupyter
# If it asks your password, check next section in this note.
```

```bash
# Create and open /home/jupyter/.ssh/authorized_keys
nano /home/jupyter/.ssh/authorized_keys
# Then paste the public key you copied in previous step here
# Ctrl + X > Y > Enter to save!
```

On your local machine,

```bash
ssh -i /Users/thi/.ssh/id_rsa.ideta jupyter@<external ip of your notebook instance>
```

You are good. **On VScode**, you make the same things with the extension **Remote - SSH**.
:::

- After running above command, you enter the instance's container (with your username, eg. when you run `whoami`, it will be `thi`) and you can also open `http://localhost:8081` for jupyer notebook on your local machine. **To stop**, type `exit` and also [[cmd]] + [[C]].
- The user (and folder) on which the notebook is running is `jupyter` (you can check `/home/jupyter/`). You can use `sudo - jupyter ` to change to this user.

::: hsbox Change password for default user and "jupyter" user

For example, default user after I connect via ssh is `thi` and the user for jupyter notebook is `jupyter`. However, you don't know their passwords. Just change them!

```bash
sudo passwd thi
# then entering the new password for this

sudo passwd jupyter
```
:::

::: hsbox (You should) Beaufity connected SSH terminal with **zsh**

**Why**? The default `bash` has a problem of "backspace" button when you connect via ssh.

👉 Note: [Zsh](/terminal/#zsh-linux).

```bash
sudo i
sudo apt install zsh # install zsh
```

Make zsh default for each user, below is for `jupyter`,

```bash
su - jupyter
chsh -s $(which zsh) # make zsh be default
exit # to log out
su - jupyter # log in again
# Then follow the instructions
```

Then, install `oh-my-zsh`,

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

You have to add conda path to `.zshrc`,

```bash
nano ~/.zshrc

# Then add the following to the end of the file
export PATH="/opt/conda/bin:$PATH"
# After that Ctrl + X > Y > Enter to save
source ~/.zshrc
```
:::

### SSH to managed notebook{:#ssh-managed}

When creating a new notebook, make sure to **enable terminal** for this notebook. Open the notebook and then open the terminal.

```bash
# On your local machine => check the public keys
cat ~/.ssh/id_rsa.pub
```

```bash
# On managed notebook, make sure you're at /home/jupyter
pwd
mkdir .ssh
touch .ssh/authorized_keys
vim .ssh/authorized_keys
# Paste the public key here
# Then save & exit (Press ESC then type :wq!)
```

```bash
# Check
cat .ssh/authorized_keys
```

```bash
# Check the external ip address of this notebook instance
curl -s http://whatismyip.akamai.com
```

Connect from local,

```bash
ssh -i ~/.ssh/id_rsa jupyter@<ip returned in previous step>
```

### AIO steps

**Remark**: This section is almost for me only (all the steps here are already described in previous steps).

::: danger

🚸 Remember to shutdown the notebook if you don't use it!!

:::

❇️ First, connect via SSH **using** `gcloud` **+ port forwarding**,

```bash
gcloud compute ssh --project <project-id> --zone <zone> <instance-name> -- -L 8081:localhost:8080
```

Open the notebook on local machine via http://localhost:8081

❇️ After below steps are done, **connect via ssh without port forwarding**,

```bash
ssh -i /Users/thi/.ssh/id_rsa.ideta jupyter@<external ip of your notebook instance>
```

❇️ Change passwords,

```bash
sudo passwd thi
sudo passwd jupyter
```

Change to user `jupyter`,

```bash
su - jupyter
```

❇️ Install **zsh**,

```bash
sudo apt install zsh
chsh -s $(which zsh)
exit
su - jupyter
# Then choose (2)
```

Install oh-my-zsh,

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

Install theme "spaceship",

```bash
git clone https://github.com/denysdovhan/spaceship-prompt.git "$ZSH_CUSTOM/themes/spaceship-prompt"
```

```bash
ln -s "$ZSH_CUSTOM/themes/spaceship-prompt/spaceship.zsh-theme" "$ZSH_CUSTOM/themes/spaceship.zsh-theme"
```

Touch zsh,

```bash
touch ~/.zshrc
```

👉 Do the same things for user `thi`!

❇️ Install Github CLI ([ref](https://github.com/cli/cli/blob/trunk/docs/install_linux.md#debian-ubuntu-linux-raspberry-pi-os-apt)),

```bash
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
```

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
```

```bash
sudo apt update
sudo apt install gh
```

Login to gh,

```bash
gh auth login
```

Then *Github.com* > SSH > *Generate a new SSH key* > *no password* > Login with a web browser

Clone the repo,

```bash
gh repo clone IdetaAutomation/ideta-logos
```

❇️ Add local ssh keys to instance machine. On local machine,

```bash
cat ~/.ssh/id_rsa.ideta.pub
# Then copy the public keys
```

On instance machine (make sure you're `jupyter` user by `whoami`),

```bash
nano /home/jupyter/.ssh/authorized_keys
# Then paste the key copied above to this and save
```

Try connect via,

```bash
ssh -i /Users/thi/.ssh/id_rsa.ideta jupyter@<external ip of your notebook instance>
```



