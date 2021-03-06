- why do I love linux?

  * so simple, yet so powerful. unix 思想有很好的一般性.

  * 小身材, 大有不同.

- 线程的目的不仅仅是为了 *同时的* 并行计算, 而是为了构建多个独立的运算单元.
  将这些运算单元分配到不同的 CPU 核上才具有 "同时并行" (parallel computing) 的意义.
  python 虽然有 GIL, 但这影响的是单 python 进程进行 parallel computing 的能力,
  并没有影响多线程所带来的其他可能性.

  所谓 concurrency, 指的就是多个任务同时处于进行中的状态, 即多任务并发.
  这可以在单个线程中实现, 例如多任务的异步处理 (e.g., python 中的 asyncio);
  也可以在多线程和多进程中实现, 以实现多个独立的运算单元.

- 为什么 shared library 具有 executable bit set?

  1. 历史原因: 一些 unix 使用 shared library 文件的 rwx 权限来对应 `mmap()`
     至进程虚拟内存中的机器码的 rwx 权限.

  2. 一些 shared library 确实可以执行, 比如 `libc.so.6`.

- bash 的 fd 255 是用来临时保留 terminal file description 的. 例如, 一个命令执行时
  需要 redirect 0, 1, 2 都至 /dev/null, bash 只能先把自己的 0, 1, 2 指向的 file
  description 即一般是 `/dev/tty?` 或 `/dev/pts/?` `dup2(2)` 至 255, 等 fork 后
  再转回来.

- 如何快速删除包含大量文件的目录?

  * 方案 1

      .. code:: sh

      mv bigdir bigdir.old
      mkdir bigdir
      rm -rf bigdir.old &
      #mkdir -p /tmp/empty
      #rsync -rd --delete /tmp/empty/ bigdir.old/ &
      #rmdir bigdir.old

    ..
    *需要了解原理并证明?*
    删除原目录还有一个附加的原因, `bigdir` 下第一层曾经可能有大量的文件, 而 dir file
    只增不减 , 即使已经空了仍然效率很差.
    使用 rsync 是因为文件系统中目录结构往往用 btree, rsync 的删除顺序避免了 btree
    rebalancing, 因此更高效. (我不理解这是啥意思?)

  * 方案 2: 把该文件系统中所有其他文件挪走, reformat 这个文件系统, 再把文件挪回来.

- Linux 中, symlink 的 permission 根本没用, stat 显示的是 ``0777``. ``chmod(2)``
  遇到 symlink 会 dereference 从而实际作用在它指向的文件上面, 根本不会去修改
  link 本身的权限. 对于 ``chmod(1)``, 若 recursively (``-R``) 修改权限, 遇到
  symlink 会直接跳过.

- 在覆盖 symlink target 时, 若 target 是一个 symlink to directory, 需要在 ``ln``
  命令中添加 ``-n``, 不让 syscall 自动 dereference link target, 否则会当作是在
  target 目录中创建一个 source 同名的连接.

    .. code:: sh
      ln -snf <source> <target>

- ``stat(1)`` 中的 ``Device: xxxh/xxxxxd`` 实际上是 device id 的 hex 和 decimal
  两种表现形式. Device id 的类型为 ``dev_t``, 最低两个 bytes 分别是 major 和 minor
  device number. major 为设备类型, 对应为 ``/proc/devices`` 里的数值, minor 为
  该类型设备的实例编号. device id 和 major/minor 之间的转换可通过 ``makedev(2)``,
  ``major(2)``, ``minor(2)``.

- ``stat(1)`` 中与文件相关的 3 个时间:

  * access time (``st_atime``): 创建文件以及访问文件内容时会更新,
    例如读取大于 0 bytes 的数据. 注意如果文件已经存在, 写数据时不会更新.

  * modified time (``st_mtime``): 创建文件以及修改文件内容时会更新, 例如
    写入大于 0 bytes 的数据. 注意文件的 metadata 方面的修改 (而不是内容的修改)
    不会导致 mtime 的改变.

  * change time (``st_ctime``): 修改文件的 metadata 时会更新.

- ``/dev/sd?`` 已经失去它原本的含义了 (sd: SCSI device). 如今, 它更像是一个泛泛的
  对存储设备的概括 (sd: storage device). 因为使用 SAS, SATA, PATA, SCSI, USB, 等协议
  和总线的设备往往都显示为 ``sd?``. 注意, 这些协议本身都是不同的, 不一定有兼容性.
  只是在 kernel 中这些 HBA 的驱动和 generic disk driver 这层通信时, 经常使用的是
  scsi 协议. 并不是说这些协议和总线与 SCSI 一定有什么从属关系.

- Shared MIME-info database 位于 ``/usr/share/mime``. ``mimetype(1)`` 命令依据这个目录
  判断文件的 MIME type. ``file(1)`` 没有使用这个 mime 数据库, 而是使用自己的
  ``magic(5)`` 格式的数据库来识别文件, 并输出这里面定义的 mime type.

  在判断 mime type 方面, 貌似 ``mimetype(1)`` 比 ``file(1)`` 要准确一些.

- ``xdg-open(1)`` 和 ``mimeopen(1)`` 的区别是:

  ``xdg-open`` 是直接和当前使用的桌面系统耦合的,
  本质上只是不同桌面系统的 open 命令的一个统一封装, 也就是说它到底调用什么程序取决于
  在 DE 中是怎么配置的, 而不一定与 mimetype 和 desktop application 的关联性有关.
  与 ``xdg-open`` 相关的配置命令是 ``xdg-mime``, 它相当于是在命令上对不同 DE 的应用和
  mimetype 的关联性的统一封装. ``xdg-mime`` 在查询和设置默认应用时也是直接访问 DE.

  ``mimeopen`` 是另一套手动的关联关系, 它不取决于当前的 DE. 而是直接读取一些确定的配置文件
  中的映射关系, 例如 ``~/.local/share/applications/defaults.list``.

- some useful options of ``file(1)`` command.

  ``-i|--mime``, ``-l|--list``, ``-p|--preserve-date``, ``-s|--special-files``

- ``cat`` command.

  - ``-A``, ``-vET``

  - ``-e``, ``-vE``

  - ``-E``, show ends

  - ``-n``, number all output lines

  - ``-t``, ``-vT``

  - ``-T``, show tabs

  - ``-v``, show nonprinting

- bash job control 中, current job (``+``) 和 previous job (``-``) 的 job spec 是
  ``%+`` (或 ``%``), ``%-``. 编号为 n 的 job 可写为 ``%n`` (或直接是 ``n``).

  而且实际上这些 ``%`` 开头的 job spec 可以直接在命令行上执行, 等价于 ``fg ...``,
  而 ``%.. &`` 则等价于 ``bg ...``.

  从这个角度看, ``%`` 可以认为是 job control 的标志符, 相应于 ``!`` 是 command history
  substitution 的标志符.

- SMBIOS/DMI 信息由 kernel 提供给 userspace 使用. 这些信息保存在 sysfs 里:
  ``/sys/firmware/dmi/tables/smbios_entry_point``
  ``/sys/firmware/dmi/tables/DMI``

- ext4 本身支持最大 1EiB 的分区, 但是比较旧的 (1.43 之前, 2016 之前) ``mke2fs`` 默认不开启
  ``64bit`` 选项, 而是 32bit, 所以最大分区只有 16TiB.

- Arch Linux features:

  * Without unnecessary additions or modifications. 软件尽量与 upstream 一致, 只做
    绝对最小量的必须的 distro-specific 的更改.

  * Rolling release of latest stable version of softwares.

  * Pragmatic. Non-free softwares are available as well as free softwares.

  * Fill the needs of those who contribute to it, rather than appealing more people.
    Arch targets competent users who enjoy its do-it-yourself nature, and who further
    exploit it to shape the system to fit their unique needs. AUR, PKGBUILD, Arch
    Build System 等为方便地安装其他软件提供了基础.

- Arch 只支持 x86-64 架构.

- Arch 里 ``/bin``, ``/sbin``, ``/usr/bin``, ``/usr/sbin`` 都是 ``/usr/bin``,
  ``/lib``, ``/lib64``, ``/usr/lib`` 都是 ``/usr/lib``.

- process virtual memory address space layout (从高位内存地址至低位内存地址)

  * kernel space

  * argv, environ

  * stack (userspace), grows downwards

    - top of stack (CPU stack pointer register -- SP)

  * unallocated memory

  * memory-mapped file, shared memory, etc.

  * unallocated memory

    - program break

  * heap, grows upwards

    - end

  * uninitialized data

    - edata

  * initialized data

    - etext

  * text (program code, CPU instruction pointer register -- IP)

  考虑到 ASLR 的存在, stack, mmap file, heap, text 四个区域的起始地址存在随机化.

- Debian/Ubuntu

  * udisks2 is patched by Ubuntu to use ``/media``, rather than default ``/run/media``.

  * distribution release with fixed software version, is a total failure. 大家
    不得不给每个软件设置一个单独的官方 apt 源. 这实在是太搞笑了. rolling release 
    显然才是合理的方式.

    对于 ubuntu, 我宁愿一直使用最新的发行版, 然后尽量只使用 ubuntu 默认提供的
    软件版本, 除非某些软件有必要使用最新的. 反正每半年升级一次系统. 至多会有
    半年的版本滞后. 对于 arch 则没事.

  * sh is symlink to dash, rather than bash.

  * /bin, /sbin, /lib 等目录不是向 /usr 目录下同名目录的 symlink, 所以仍然存在
    / 和 /usr 目录程序的无意义区分.

  * debian 给 pip 打了 patch, 不能删除用 apt 安装的 python module.

  * debian 为了保证 system python 以及 modules 的版本是 package manager 里确定的,
    给 python interpreter 打 patch, 让所有全局 modules 安装到所谓 ``dist-packages``.
    这号称是为了避免和编译的 python 的 ``site-packages`` 冲突. 虽然我没看出来
    怎么会有人把手编的 python 的库放到 ``/usr/lib`` 中.

    如果在 virtual env 中, 则会安装到 ``site-packages`` 中.

  * Ubuntu 17.10:

    - GNOME 替代 Unity.

    - Wayland 替代 Mir.

    - GDM 替代 lightDM.

    - Window control buttons are back on the right for the first time since 2010.

    - python2 not installed by default. python3 upgraded to 3.6.

  * apt.

    - ``apt install`` 安装一个 package 时, 在 unpacking 之后的 setup 阶段, 它懂得
      先 setup package 的所有依赖, 再 setup package 本身, 从而解决了依赖问题.

- kernel 默认给出的设备名称是十分 generic 的. 它根据设备的类型以及发现顺序进行
  编号, 生成如 ``eth<N>``, ``sd<X><N>`` 等设备类型 + 编号的名字. 这样命名的问题
  是系统中看到的设备逻辑名称与其物理身份无法直接对应起来. 只能通过 sysfs 来研究
  对应的设备到底是哪个. systemd-udev 使用了一种全新的设备命名规则, 称为 predictable
  name, 使用设备的类型和物理身份等信息来构建逻辑名称. 它遵循的逻辑为:

  * network interface::

      en - Ethernet
      sl - serial line IP (slip)
      wl - wlan
      ww - wwan

      o<index>[n<phys_port_name>|d<dev_port>]
         - on-board device index number
           (主板继承, 而不是通过 PCIe bus)
      s<slot>[f<function>][n<phys_port_name>|d<dev_port>]
         - hotplug slot index number
           (插槽位置, 以及一个设备可能提供多个功能 multi-function device)
      x<MAC>
         - mac address
      [P<domain>]p<bus>s<slot>[f<function>][n<phys_port_name>|d<dev_port>]
         - PCI geographical location
           (PCIe 总线地址, 总线上的插槽地址, 以及一个设备可能有多个功能.
            只有 PCI domain 不是 0 时才有 domain 部分.)
      [P<domain>]p<bus>s<slot>[f<function>][u<port>][..][c<config>][i<interface>]
         - USB port number
           (USB bus 一般是 PCIe bus 的下游, 通过 USB host controller 来衔接.
            所以首先包含 PCIe 地址. USB 可能存在多级 hub, 所以是 uXuX.. 的形式.)

    例子:

    USB built-in 3G modem::

      /sys/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.4/2-1.4:1.6/net/wwp0s29u1u4i6
      ID_NET_NAME_MAC=wwx028037ec0200
      ID_NET_NAME_PATH=wwp0s29u1u4i6

    PCI Ethernet multi-function card with 2 ports::

      /sys/devices/pci0000:00/0000:00:1c.0/0000:02:00.0/net/enp2s0f0
      ID_NET_NAME_MAC=enx78e7d1ea46da
      ID_NET_NAME_PATH=enp2s0f0
      /sys/devices/pci0000:00/0000:00:1c.0/0000:02:00.1/net/enp2s0f1
      ID_NET_NAME_MAC=enx78e7d1ea46dc
      ID_NET_NAME_PATH=enp2s0f1

- Why GNOME switched to dconf, which is binary configuration rather than
  plain text file?

  For performance reasons. The start up sequence becomes a bottleneck
  pretty quickly if you don't have a mmap()'able cache, and keeping
  cache + text in sync is a major headache.

  The problem is not when one program does it. if we only had one program,
  of couse we'd be using a text storage. The issues arise when there are many
  programs, many of which may start concurrently. then reading those files, if
  they are not kept contiguous on disk, becomes an issue on spinning rust hardware.

  Watching a single file for changes is better than watching multiple files.

  ref: https://www.reddit.com/r/linux/comments/2q2wv6/plain_text_configuration_of_gnome/

- lock a user: ``passwd -l user``.
  unlock a user: ``passwd user`` simply change its password.
  remove user password: ``passwd -d user``.

- root account: 应该禁止以 root 身份远程登录, 但不要 lock root account 以至于都不能
  从本地使用 root 登录 (login, su, sulogin, etc.). 因为在 rescue mode 我们需要
  root login.

- 向进程发送 SIGSTOP, SIGTSTP 等 signal, 或者在 shell 中 ctrl-z foreground process
  会把进程置于 stop 状态, 再发送 SIGCONT 可以恢复运行.

- 进程可以对 SIGTERM & SIGQUIT 的处理进行区分. 前者相当于告知程序立即退出, 后者是
  告知程序把现在正在做的事情做完就退出. 参考 nginx 的处理.

- buffer and cache.

  * buffer: A storage used to temporarily store data while it is being moved from
    one place to another.
    
    使用 buffer 的目的是为了提高不同速率的组件之间的数据传输效率. 当发送方和接受方
    在数据速率上有差异 (一般是接受方比发送方慢) 时, 需要一个缓冲层, 暂时放置那些尚未
    处理的数据, 避免发送方每次发数据时都要 blocking 等待所有数据接受完毕. 这样就提高
    了不同速度的组件协同工作时的效率.

    例如, OS 一般实现了 filesystem buffer. userspace app 只需写数据到 kernel
    fs buffer 即可返回执行下一指令, 无需 blocking 等待内核真把数据写入硬盘.

  * cache: A storage used to temporarily store data which is very probable to be
    accessed again.

    使用 cache 的目的是减少向慢存储的访问次数, 从而提高数据访问效率. 它的应用场景
    是相同的数据需要多次读取时. 此外, 在设计 IO API 时, cache 层应该是对用户透明的,
    即用户直接调用向真实存储设备的 IO API, 而无需知道 cache 层的存在, cache 自动生效.

- calculator: bc vs dc.

  * dc: It is one of the oldest Unix utilities, predating even the invention of the
    C programming language; like other utilities of that vintage, it has a
    powerful set of features but an extremely terse syntax.

    dc 使用 reverse polish notation. 非常反直觉.

  * bc: POSIX-standardized, 稍微现代化一些, 比 dc 直观很多. bc 算是一门计算语言和
    解释器. Traditionally bc was only a front end tool that compiled the bc
    notation to the notation of dc and piped that into dc to get the result.
    但如今并不是这样. bc 自己计算结果.

- why adding a new group to current user needs a re-login to have effect?

  内核 (通过一定的 userspace 程序例如 login) 给进程赋予权限, 这权限体现为赋予该进程
  一系列的 uids 和 gids. 一般情况下, 一个进程不能修改赋予自己的组. 所以虽然修改了
  ``/etc/group``, 但当前进程例如 shell 的权限不受影响, 所以它新开的子进程继承的权限
  仍然是旧的权限. 所以需要重新登录.

  基于同样的原理, 使用 ``newgrp`` command 可以在不重新登录的情况下修改 group. 这本质
  上就是重新认证给新进程加新的组. 注意 newgrp is setuid-root.

- NSS (Name Service Switch). NSS 整合了各种 name services 即 directory services,
  相当于一个统一的 API. 这包括 DNS, LDAP, 等等.
