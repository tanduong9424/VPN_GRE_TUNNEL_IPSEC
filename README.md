# Cấu hình VPN Gre Tunnel kết hợp IPSec
## Nội dung
  - **I. Cấu hình trên GNS3**
    - **1. Cấu hình OSPF**
    - **2. Cấu hình Tunnel**
    - **3. Cấu hình Static Route cho Tunnel**
    - **4. Cấu hình IPSec**
  - **II. Thực hiện thông qua dây mạng LAN kết nối 2 thiết bị**
    - **1. Cấu hình Tunnel**
    - **2. Cấu hình Static Route cho Tunnel**
    - **3. Cấu hình IPSec**
    
 
  ## I. Cấu hình trên GNS3
  ### 1. Cấu hình OSPF
  - Với sơ đồ mạng dưới, ta thực hiện cấu hình OSPF trên 8 router với Process ID OSPF là 1, Area là 100.
  - ![](./hinh1.png)
  - Trên R1
  ```
        SITE-A# conf t
        SITE-A(config)# router ospf 1
        SITE-A(config)# net 192.168.10.0 0.0.0.255 area 100
        SITE-A(config)# net 10.0.90.0 0.0.0.255 area 100
  ```
  - Trên R2
  ```
        SITE-B# conf t
        SITE-B(config)# router ospf 1
        SITE-B(config)# net 192.168.20.0 0.0.0.255 area 100
        SITE-B(config)# net 10.0.100.0 0.0.0.255 area 100
  ```
  - Trên R3
  ```
        R3#conf t
        R3(config)# router ospf 1
        R3(config)# net 10.0.0.0 0.0.0.255 area 100
        R3(config)# net 10.0.10.0 0.0.0.255 area 100
  ```
  - Trên R4
  ```
        R4#conf t
        R4(config)# router ospf 1
        R4(config)# net 10.0.0.0 0.0.0.255 area 100
        R4(config)# net 10.0.20.0 0.0.0.255 area 100
        R4(config)# net 10.0.40.0 0.0.0.255 area 100
        R4(config)# net 10.0.90.0 0.0.0.255 area 100
   ```
  - Trên R5
  ```
        R5#conf t
        R5(config)# router ospf 1
        R5(config)# net 10.0.10.0 0.0.0.255 area 100
        R5(config)# net 10.0.30.0 0.0.0.255 area 100
        R5(config)# net 10.0.50.0 0.0.0.255 area 100
        R5(config)# net 10.0.100.0 0.0.0.255 area 100
  ```
  - Trên R6
  ```
        R6#conf t
        R6(config)# router ospf 1
        R6(config)# net 10.0.20.0 0.0.0.255 area 100
        R6(config)# net 10.0.30.0 0.0.0.255 area 100
        R6(config)# net 10.0.60.0 0.0.0.255 area 100
        R6(config)# net 10.0.70.0 0.0.0.255 area 100
  ```
  - Trên R7
  ```
        R7#conf t
        R7(config)# router ospf 1
        R7(config)# net 10.0.40.0 0.0.0.255 area 100
        R7(config)# net 10.0.60.0 0.0.0.255 area 100
        R7(config)# net 10.0.80.0 0.0.0.255 area 100
  ```
  - Trên R8
  ```
        R8#conf t
        R8(config)# router ospf 1
        R8(config)# net 10.0.50.0 0.0.0.255 area 100
        R8(config)# net 10.0.70.0 0.0.0.255 area 100
        R8(config)# net 10.0.80.0 0.0.0.255 area 100
  ```
  ### 2. Cấu hình Tunnel
  - Thực hiện tạo Tunnel trên 2 router R1 và R2
  - Trên R1 ta cấu hình Tunnel như sau:
  ```
      SITE-A# conf t
      SITE-A(config)# int tunnel 1
      SITE-A(config)# ip address 192.168.0.1 255.255.255.0
      SITE-A(config)# tunnel source 10.0.90.1
      SITE-A(config)# tunnel destination 10.0.100.1
      SITE-A(config)# end
  ```
  - Trên R2 ta cấu hình Tunnel như sau:
  ```
      SITE-B# conf t
      SITE-B(config)# int tunnel 1
      SITE-B(config)# ip address 192.168.0.2 255.255.255.0
      SITE-B(config)# tunnel source 10.0.100.1
      SITE-B(config)# tunnel destination 10.0.90.1
      SITE-B(config)# end
  ```
  ### 3. Cấu hình Static Route cho Tunnel
  - Ở Router R1 ta cấu hình Static Route để đi vào Site A thông qua Tunnel như sau:
  ```
      SITE-A# conf t
      SITE-A(config)# ip route 192.168.20.0 255.255.255.0 192.168.0.2
  ```
  - Ở Router R2 ta cũng cấu hình Static Route tưởng tự để đi vào Site A như sau:
  ```
      SITE-B# conf t
      SITE-B(config)# ip route 192.168.20.0 255.255.255.0 192.168.0.1
  ```
  ### 4. Cấu hình IPSec
  - Ta tiến hành chia việc cấu hình ra 2 phase gồm Phase 1 nhằm xác thực kết nối và Phase 2 tiến hành mã hóa. Ở trong Phase sẽ tiến hành 2 cách cấu hình là Crypto Map được cài đặt trên cổng Serial 2/1 của router R1 và IPSec Profile được cài đặt trên Tunel 1.
  - Đầu tiên Phase 1 trên router R1 được cấu hình như sau:
  ```
    SITE-A# conf t
    SITE-A(config)# crypto isakmp policy 10
    SITE-A(config-isakmp)# encryption aes 256
    SITE-A(config-isakmp)# hash md5
    SITE-A(config-isakmp)# authentication pre-share
    SITE-A(config-isakmp)# group 2
    SITE-A(config-isakmp)# lifetime 3600
    SITE-A(config-isakmp)# exit
    SITE-A(config)# crypto isakmp key password address 10.0.100.1
  ```
  - Kế tiếp Phase 1 trên router R2 được cấu hình như sau:
  ```
    SITE-B# conf t
    SITE-B(config)# crypto isakmp policy 10
    SITE-B(config-isakmp)# encryption aes 256
    SITE-B(config-isakmp)# hash md5
    SITE-B(config-isakmp)# authentication pre-share
    SITE-B(config-isakmp)# group 2
    SITE-B(config-isakmp)# lifetime 3600
    SITE-B(config-isakmp)# exit
    SITE-B(config)# crypto isakmp key password address 10.0.90.1
  ```

  - Với Phase 2 trên R1 ta cấu hình Crypto Map:
  ```
    SITE-A(config)# crypto ipsec transform-set GRE esp-aes 256 esp-md5-hmac 
    SITE-A(cfg-crypto-trans)# mode transport
    SITE-A(cfg-crypto-trans)# exit
    SITE-A(config)# ip access-list extended GRE-ACL
    SITE-A(config-ext-nacl)# permit gre host 10.0.90.1 host 10.0.100.1
    SITE-A(config-ext-nacl)# exit
    SITE-A(config)# crypto map GRE-CMAP 10 ipsec-isakmp
    SITE-A(config-crypto-map)# match address GRE-ACL
    SITE-A(config-crypto-map)# set transform-set GRE
    SITE-A(config-crypto-map)# set peer 10.0.100.1
    SITE-A(config-crypto-map)# exit
  ```
  Bước cuối cùng của Phase 2 trên R1 là thiết lập Crypto Map lên Interface thích hợp
  ```
    SITE-A(config)# int Serial2/1
    SITE-A(config-if)# crypto map GRE-CMAP
  ```
  - Về Phase 2 trên R2 ta cấu hình IPSec Profile:
  ```
    SITE-B(config)# crypto ipsec transform-set GRE esp-aes 256 esp-md5-hmac
    SITE-B(cfg-crypto-trans) mode transport
    SITE-B(cfg-crypto-trans)#exit
    SITE-B(config)#crypto ipsec profile GRE-PROFILE
    SITE-B(ipsec-profile) set transform-set GRE
    SITE-B(ipsec-profile)# exit
  ```
  Bước cuối cùng của Phase 2 trên R2 là thiết lập IPSec Profile lên Tunnel 1
  ```
    SITE-B(config)# int tun1
    SITE-B(config-if)# tunnel protection ipsec profile GRE-PROFILE
  ```
