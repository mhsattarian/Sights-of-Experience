# Sights of Experience!

Instructions I found helpful to install various packages and softwares on my Manjaro linux.

## Linux Issues

### tty not found!

<details>

<summary>checkout: Boot livedisk + chroot + update + mkinitcpio</summary>

<br>


I take it that this means you cannot boot into prompt mode at all. Which means you have to chroot into the installed partition and make repairs to it. But the situation does not look promising.

[1] - I rarely use chroot and these are the steps I used.
There are several (okay,2) simpler ways to chroot but I have never used them.
Perhaps if others step in on chroot, use their procedure. It’s simpler.
Here are my steps:

```shell
mount /dev/sda3 /mnt
cd /mnt
mount -t proc proc /mnt/proc
mount -t sysfs sys /mnt/sys
mount -o bind /dev /mnt/dev
mount -t devpts pts /mnt/dev/pts/
chroot /mnt
```

I used `/dev/sda3` as an example, Use the correct Manjaro partition in your case.

Chroot [1] from a live cd and perform these operations at chroot

```shell
sudo pacman-mirrors -g
sudo pacman -Syyu
sudo mkinitcpio -P
```

When finished with chroot, type

```shell
exit
```

And then don’t do anything else, just in case chroot is still in operations. **Reboot**. Good luck.


</details>

## Machine Learning Installations

### Opencv

<details>
<summary>summary : Install opencv-python from pip</summary>

<br>

after installing conda, I tried to install opencv using command:


```shell
  conda install -c conda-forge opencv
```
the problem was it didn't have the GUI libs necessary, so it would error something like:

```shell
The function is not implemented. Rebuild the library with Windows, GTK+ 2.x ... install libgtk2.0-dev and pkg-config, then re-run cmake ...
```

so I searched the web, and tried mapno channel's opencv from conda, and also installing gtk2 and pkg-config packages didn't help.
the solution for me was to install `opencv-python` package from `pip`:

```shell
sudo pip install opencv-python
```


</details>

### Tensorflow GPU

<details>
<summary>summary : Install tensorflow-gpu from conda + read <a href="https://antergos.com/wiki/hardware/bumblebee-for-nvidia-optimus/">here</a> and <a href="https://stackoverflow.com/a/44786736">here</a> </summary>

<br>

Manjaro linux repositories have `Tensorflow-cuda` and `python-tensorflow-cuda` packages, unfortunately none of them worked for me cause my manjaro had `python 3.7` and also installed `Anaconda` package had the same version and as of now Tensorflow is not supporting the 3.7 version. Installing tensorflow gpu from pip would prompt an error like so :

```shell
Could not find a version that satisfies the requirement tensorflow-gpu (from versions: )
No matching distribution found for tensorflow-gpu
```

This is how I finnaly installed it:

First I created a new environment unsing conda with python 3.6:

```shell
conda create -n myenv python=3.6
```

Install GPU drivers:

```shell
sudo mhwd -a pci nonfree 0300
```


I installed `Cuda` and `Cudnn` using:

```shell
sudo pacman -S cuda cudnn
```

then I installed `tensorflow-gpu` package from the manjaro repos (probebly won't help anything but thats what I did). 

finnaly I tried to install `tensorflow-gpu` from conda using:

```shell
conda install tensorflow-gpu
```

and It solved it!

for Adding the virtual environment to supported kernels of jupyter notebook use:

```shell
python -m ipykernel install --user --name [deep-learning] --display-name "Python (deep-learning)"
```
[+ more info](https://stackoverflow.com/a/44786736)

after running a python notebook and testing, tensorflow would import and work fine, but **it wasn't using the GPU**. in order to solve that I followed [this Instruction](https://antergos.com/wiki/hardware/bumblebee-for-nvidia-optimus/#). luckily it should start your GPU:

```shell
sudo optirun -b none nvidia-settings -c :8
```

</details>

### Sonddevice

<details >
<summary>summary : Install portaudio python-pyaudio using your package manager</summary>

<br>

Installing sounddevice from pip didn't work and trough this error:

```bash
 OSError: PortAudio library not found
```

Solution (oddly I didn't see it elsewhere) was to install PortAudio library and its python wrapper:

```bash
pacman -Syu portaudio python-pyaudio 
```

</details>

### Converting ipython notebooks to scripts

<details>
<summary>summary : use jupyter nbconvert</summary>

<br>

```bash
jupyter nbconvert --to python *.ipynb --TemplateExporter.exclude_markdown=True --TemplateExporter.exclude_input_prompt=True
```

</details>

