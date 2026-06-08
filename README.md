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
<img width="525" height="368" alt="图片" src="https://github.com/user-attachments/assets/5c279783-491d-48d8-85af-4a55dbc1500b" />

Here, I randomly picked port 12778 and added a port forwarding rule in VirtualBox.

<img width="414" height="175" alt="图片" src="https://github.com/user-attachments/assets/efd172c2-1ac1-400c-b53c-06737e16a3ec" />

But I still couldn't access it. That's because the Hadoop sandbox uses nested virtualization(a docker inside a VM).

<img width="416" height="276" alt="图片" src="https://github.com/user-attachments/assets/80782d95-a74b-4b0a-a5b3-7aee4cdabef1" />

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

<img width="574" height="303" alt="图片" src="https://github.com/user-attachments/assets/73f40342-ceb9-4f52-b035-256d64df2483" />

Here, I mapped port 12778 inside Docker to port 1234 on Windows. Now I can access the 12778 service inside the Hadoop Docker container through port 1234 on Windows.

<img width="988" height="535" alt="图片" src="https://github.com/user-attachments/assets/7b26e857-9cd4-40fd-b994-40f3bcf28a17" />


P.S.

Actually, you can use port 8888 start service and access the notebook — it's pre-opened by Docker. You can just open `127.0.0.1:8888` in your Windows browser to see the notebook page inside the VM. But the code won't run, because it uses WebSocket connections. Probably due to multiple layers of network translation and cross-origin restrictions, the code cells just can't execute.
<img width="2119" height="580" alt="图片" src="https://github.com/user-attachments/assets/9591eb45-d345-4024-888f-e4d60cd91613" />
if you open web console,you can see
<img width="1086" height="180" alt="图片" src="https://github.com/user-attachments/assets/23c8dc7e-4078-43c9-8532-c09aefed2c1d" />
