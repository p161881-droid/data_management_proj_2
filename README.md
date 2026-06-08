# data_management_proj_2


# Setting Up Jupyter Notebook in Hadoop Sandbox
First, install Jupyter Notebook in the Hadoop sandbox:

```bash
#1. Make sure pip version is compatible(20.3.1 is the last version that supports Python 2.7)
pip install --upgrade 'pip==20.3.1'

#2. Upgrade setuptools to the latest version compatible with Python 2.7
pip install --upgrade 'setuptools==44.1.1'

#3. Use --ignore-installed to force install notebook
pip install --ignore-installed 'notebook==5.7.16'
```

Then, use Jupyter Notebook instead of the Python interpreter to start PySpark:

```bash
PYSPARK_DRIVER_PYTHON=jupyter PYSPARK_DRIVER_PYTHON_OPTS="notebook --ip=0.0.0.0 \
    --port=12778\
    --no-browser \
    --allow-root \
    --NotebookApp.token='' \
    --NotebookApp.allow_origin='*' \
    --NotebookApp.allow_remote_access=True \
    --NotebookApp.disable_check_xsrf=True \
    --NotebookApp.password_required=False" \
Pyspark
```
<img width="572" height="401" alt="图片1" src="https://github.com/user-attachments/assets/8410b4e0-1e4b-401c-8b22-4e7c5aca448f" />

Here, I randomly picked port 12778 and added a port forwarding rule in VirtualBox.

<img width="413" height="175" alt="图片2" src="https://github.com/user-attachments/assets/165263d0-ca6d-4eea-97dc-723d682627b7" />


But I still couldn't access it. That's because the Hadoop sandbox uses nested virtualization(a docker inside a VM).

<img width="413" height="274" alt="图片3" src="https://github.com/user-attachments/assets/dc986c07-438d-4849-83cb-33f2a1467b6c" />


VirtualBox is a virtual machine, and the Hadoop sandbox is a VM inside a VM — it actually runs in Docker. And in Docker, you have to explicitly specify port mappings, like this:

```bash
docker run -d \
  --name hadoop \
  -p 2222:22 \
  -p 1080:1080 \
Image_name
```
But we can't change the Docker port mapping because the container is already running.

Except for those specific ports, all other ports (like 12778) are unusable and can't be accessed from outside Docker. 

If you want to use port 12778, you need to set up an SSH tunnel in Windows CMD to map port 12778 from inside Docker to the outside world.

<img width="493" height="260" alt="图片4" src="https://github.com/user-attachments/assets/b969c88d-fed8-48b9-8927-411c02457594" />


Here, I mapped port 12778 inside Docker to port 1234 on Windows. Now I can access the 12778 service inside the Hadoop Docker container through port 1234 on Windows.

<img width="432" height="365" alt="图片6" src="https://github.com/user-attachments/assets/6e7cee65-2758-4639-891d-684ca7fbcc79" />


P.S.

Actually, you can use port 8888 start service and access the notebook — port 8888 is pre-opened by Docker. You can just open `127.0.0.1:8888` in your Windows browser to see the notebook page inside the VM. But the code won't run, because code uses WebSocket connections. Probably due to multiple layers of network translation and cross-origin restrictions, the code cells just can't execute.
<img width="2119" height="580" alt="图片" src="https://github.com/user-attachments/assets/9591eb45-d345-4024-888f-e4d60cd91613" />
if you open web console,you can see
<img width="1086" height="180" alt="图片" src="https://github.com/user-attachments/assets/23c8dc7e-4078-43c9-8532-c09aefed2c1d" />


# How to check if hadoop-sandbox is running in Docker?

When the system boots up, you can clearly see that it automatically enters the Docker virtual machine.

<img width="404" height="251" alt="图片5" src="https://github.com/user-attachments/assets/d72d40c4-88b8-43b1-ad9d-d85cadefb372" />

If you want to stay in the real host, you can set the network to bridged mode instead of NAT. Docker will fail to start, and after the failure, you'll be left in the real host machine.

Like this — we're logged into the real host `@sandbox-host`, not the Docker container `@sandbox-hdp`.From here, we can check the Docker version:

<img width="407" height="266" alt="图片7" src="https://github.com/user-attachments/assets/477e875f-4d9b-4f45-a692-746eaaef078f" />


```bash
docker --version
```
The version is **18.03.0**.

And list the Hadoop sandbox images:

```bash
docker images
```

<img width="442" height="308" alt="图片8" src="https://github.com/user-attachments/assets/c67f1ab5-1aaf-42d8-bbf9-df7db2c01b50" />

There are two images:

- `hortonworks/sandbox-proxy` — 1.0 — 109MB
- `hortonworks/sandbox-hdp-security` — 2.6.5 — 21.3GB
