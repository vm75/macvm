# macvm

* KVM may need the following tweak on the host machine to work.

  ```
  sudo modprobe kvm; echo 1 | sudo tee /sys/module/kvm/parameters/ignore_msrs
  ```

  To make this change permanent, you may use the following command.

  ```
  sudo cp kvm.conf /etc/modprobe.d/kvm.conf  # for intel boxes only

  sudo cp kvm_amd.conf /etc/modprobe.d/kvm.conf  # for amd boxes only
  ```

* Add user to the `kvm` and `libvirt` groups (might be needed).

  ```
  sudo usermod -aG kvm $(whoami)
  sudo usermod -aG libvirt $(whoami)
  sudo usermod -aG input $(whoami)
  ```
  Note: Re-login after executing this command.

* Fetch macOS installer.

  ```
  ./fetch-macOS-v2.py
  ```
  You can choose your desired macOS version here. After executing this step,
  you should have the `BaseSystem.dmg` file in the current folder.

  ATTENTION: Let `>= Big Sur` setup sit at the `Country Selection` screen, and
  other similar places for a while if things are being slow. The initial macOS
  setup wizard will eventually succeed.

* Convert the downloaded `BaseSystem.dmg` file into the `BaseSystem.img` file.

  ```
  dmg2img -i BaseSystem.dmg BaseSystem.img
  ```

* Create a virtual HDD image where macOS will be installed. If you change the
  name of the disk image from `mac_hdd_ng.img` to something else, the boot scripts
  will need to be updated to point to the new image name.

  ```
  qemu-img create -f qcow2 mac_hdd_ng.img 256G
  ```

  NOTE: Create this HDD image file on a fast SSD/NVMe disk for best results.

### Headless macOS

- Use the provided [boot-macOS-headless.sh](./boot-macOS-headless.sh) script.

  ```
  ./boot-macOS-headless.sh
  ```

### libvirt vm
- Use this macOS VM disk with libvirt (virt-manager / virsh stuff).

  - Edit `macos.xml` file and change the various file paths

    ```
    virt-xml-validate macos.xml
    ```

  - Create a VM by running the following command.

    ```bash
    virsh --connect qemu:///system define macOS.xml
    ```

  - If needed, grant necessary permissions to libvirt-qemu user,

    ```
    sudo setfacl -m u:libvirt-qemu:rx /home/$USER
    sudo setfacl -R -m u:libvirt-qemu:rx /home/$USER/OSX-KVM
    ```

  - Launch `virt-manager` and start the `macOS` virtual machine.


# repositories
* https://github.com/kholia/OSX-KVM.git
* https://github.com/royalgraphx/DarwinKVM.git
* https://github.com/matus-sabo/KVM.git
* https://github.com/thenickdude/KVM-Opencore.git
* https://github.com/foxlet/macOS-Simple-KVM.git

# references
* https://sick.codes/how-to-install-macos-virtual-machine-on-linux-arch-manjaro-catalina-mojave-or-high-sierra-xcode-working/
* https://www.kraxel.org/blog/2017/09/running-macos-as-guest-in-kvm/
* https://github.com/thenickdude/KVM-Opencore
* https://passthroughpo.st/new-and-improved-mac-os-tutorial-part-1-the-basics/
* https://github.com/matus-sabo/KVM
* https://docs.darwinkvm.com/
* https://github.com/royalgraphx/DarwinKVM
* https://ostechnix.com/how-to-find-best-ubuntu-apt-repository-mirror/
* https://github.com/cockpit-project/cockpit/issues/12431
* https://wiki.archlinux.org/title/network_bridge#With_NetworkManager
