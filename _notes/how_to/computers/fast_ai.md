# fast.ai course notes

### AWS

* [VLR AWS tutorial](https://docs.google.com/presentation/d/1yNztk6hXp_sQwZF42SpPcAfgHQ-yVGQwp-l5wKbFtmY/edit#slide=id.g33283fda8f_1_26)
* Launching an instance: https://aws.amazon.com/getting-started/tutorials/launch-a-virtual-machine/
    * `ssh -i ~/.ssh/mykeypair.pem ec2-ubuntu@{Public DNS}`, check dashboard
    * **Remember to terminate instance after done**

* Running jupyter notebook: https://docs.aws.amazon.com/dlami/latest/devguide/setup-jupyter-configure-server.html
    * Port forwarding from 8157 of local to 8888 on AWS instance: `ssh -i ~/mykeypair.pem -L 8157:127.0.0.1:8888 ubuntu@[public DNS]`
    * Jupyter notebook on local machine at: `http://127.0.0.1:8157`. Might need to do `http://127.0.0.1:8157/?=[token]`

##### New instance procedure

1. Create EBS volume
    * For assignment 1 suggested 20GB 
    * Ensure that it’s the same availability region as the instance
2. Make the volume available on the instance
    * `lsblk`will show you the attached devices (find the name of the attached EBS volume)
    * `sudo file -s /dev/<device_name>` will show whether the device has a filesystem
    * `sudo mkfs -t ext4 /dev/<device_name>` will create the ext4 file system on the device (_do this only if the previous command returned “data”_) 
    * `sudo mkdir <mount_point>` make a folder for mounting the volume
    * `sudo mount /dev/<device_name> <mount_point>` will mount the volume
    * `sudo chmod 777 -R <mount_point>` Add permissions to access the folder
    * `df -h` will show you the usage on the disks
3. Work on stuff in the mounted directory. 
4. Backup the volume using snapshots

### Numpy

* `A.T` : short notation for transpose
* `A@x` : short notation for `dot` matrix-vector or matrix-matrix multiply

* `np.set_printoptions(suppress=True, precision=4)` to zero out epsilons, precision is number of digits

### Python

* Checking memory usage:
```
import psutil
def mem_usage():
    process = psutil.Process(os.getpid())
    return process.memory_info().rss / psutil.virtual_memory().total
```

* Progress bar in for-loops:
```
from tqdm import tqdm
for i in tqdm(range(10)):
    sleep(0.2)
```

* [3D plots with matplotlib](https://jakevdp.github.io/PythonDataScienceHandbook/04.12-three-dimensional-plotting.html)

### Jupyter notebooks

* [Add beep for long sections](https://gist.github.com/iandanforth/fd9c98dc485592e354974e394ff6ae28)
* `%time` to time one run of section, `%timeit` to run section multiple times and get mean/std
* hit `h` to see available keyboard shortcuts
* `?[python command]` to see doc
* Built-in magic commands:
    * List all of them : `%lsmagic`
    * Time : `%time` for one run one line, `%%time` for one run whole cell, `%timeit` to run 100,000 times and take mean of three fastest times
    * https://www.dataquest.io/blog/jupyter-notebook-tips-tricks-shortcuts/
    * http://ipython.readthedocs.io/en/stable/interactive/magics.html
* Execute shell commands : `![shell command]`

### AWS

_User info_

kazu
AKIAJ2D7P2XKXY4CZICA
RquvpvUvZrVyQU2T94qcG0gxfUWPxmhEZJ+KjsRo

_EC2 instance_

p2:

```
Find all you need to connect in the fast-ai-commands.txt file and to remove the stack call fast-ai-remove.sh
Connect to your instance: ssh -i /Users/Kazu/.ssh/aws-key-fast-ai.pem ubuntu@ec2-34-250-34-195.eu-west-1.compute.amazonaws.com
```

t2-micro:

```
ssh -i /Users/Kazu/.ssh/aws-key-fast-ai.pem ubuntu@ec2-52-214-234-185.eu-west-1.compute.amazonaws.com
```

### Other tricks

* `ec2-34-250-34-195.eu-west-1.compute.amazonaws.com:8888`, dl_course

_tmux_

* `ctrl+b [arrow]` to switch panes
* `ctrl+b "` to make a new pane
* `ctrl+b ?` to see possible keyboard shortcuts
