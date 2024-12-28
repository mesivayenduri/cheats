--> sudo apt install virt-manager

--> testmachine.xml

<domain type="qemu">
    <name>TestMachine</name>
    <memory unit="GiB">1</memory>
    <vcpu>1</vcpu>
    <os>
        <type arch="x86_64">hvm</type>
    </os>
</domain>

--> virsh define testmachine.xml

--> virsh list

--> virsh list --all

--> virsh reset TestMachine

--> virsh dominfo TestMachine

--> virsh start TestMachine

--> virsh shutdown TestMachine

--> virsh destroy TestMachine

--> virsh setvcpus TestMachine 2 --config

--> virsh setvcpus TestMachine 2 --config --maximum

--> virsh undefine --remove-all-storage TestMachine

# ANY HELP WITH ANY OPTION

--> virsh help <option>

Eg: virsh help setvpucs
    virsh help undefine


--> virsh autostart TestMachine

--> virsh autostart --disable TestMachine

--> virsh setmaxmem TestMachine 2048M --config

--> virsh setmem TestMachine 2048M --config



--> qemu-img info name.img

--> qemu-img resize name.img 10G

--> ls /var/lib/libvrt/

--> cp name.img /var/lib/libvrt/images/

--> virt-install --osinfo ubuntu22.04 --name ubuntu1 --memory 1024 --vcpus 1 --import --disk /var/lib/libvirt/images/ubuntxxxx.img --graphics none --cloud-init root-password-generate=on




--> virt-install --osinfo debian12 --name debian1 --memory 1024 --vcpus 1 --disk size=10 --location /var/lib/libvirt/boot/debian12-netinst.iso --graphics none --extra-args "console=ttyS0"


--> virt-install --osinfo debian12 --name debian1 --memory 1024 --vcpus 1 --disk size=10 --location https://deb.debian.org/debian/dists/bookworm/main/installer-amd64/ --graphics none --extra-args "console=ttyS0"








