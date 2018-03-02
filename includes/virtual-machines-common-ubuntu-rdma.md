1. Installera dapl, rdmacm, ibverbs och mlx4

  ```bash
  sudo apt-get update

  sudo apt-get install libdapl2 libmlx4-1

  ```

2. Aktivera RDMA i /etc/waagent.conf, av uncommenting följande konfigurationsrader. Du måste rotåtkomst att redigera filen.
  
  ```
  OS.EnableRDMA=y

  OS.UpdateRdmaDriver=y
  ```

3. Lägg till eller ändra följande minnesinställningar i KB i filen /etc/security/limits.conf. Du måste rotåtkomst att redigera filen. Du kan ange memlock till obegränsad för testning. Till exempel: `<User or group name>   hard    memlock   unlimited`.

  ```
  <User or group name> hard    memlock <memory required for your application in KB>

  <User or group name> soft    memlock <memory required for your application in KB>
  ```
  
4. Installera Intel MPI-biblioteket. Antingen [köpa och ladda ned](https://software.intel.com/intel-mpi-library/) bibliotek från Intel eller hämtning av [kostnadsfri utvärderingsversion](https://registrationcenter.intel.com/en/forms/?productid=1740).

  ```bash
 wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
 Endast Intel MPI 5.x körningar stöds.
 
 Anvisningar för installation finns den [Intel MPI biblioteket installationsguiden](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Aktivera ptrace för icke-felsökare processer (behövs för de senaste versionerna av Intel MPI).
 
  ```bash
  echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
  ```
