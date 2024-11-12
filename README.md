# Cấu hình VPN Gre Tunnel kết hợp IPSec
## Nội dung
  - **I. Cấu hình trên GNS3**
    - **1. Cấu hình OSPF**
    - **2. Cấu hình Tunnel**
    - **3. Cấu hình Static Route cho Tunnel**
    - **4. Cấu hình IPSec**
  - **II. Thực hiện thông qua dây mạng LAN kết nối 2 thiết bị**
    - **1. Cấu hình OSPF**
    - **2. Cấu hình Tunnel**
    - **3. Cấu hình Static Route cho Tunnel**
    - **4. Cấu hình IPSec**
    
 
  ## I. Cấu hình trên GNS3
  ### 1. Cấu hình OSPF
  - Thực hiện cấu hình OSPF trên 8 router với Process ID OSPF là 1, Area là 100
  - Trên R1
  - ```
        SITE-A# conf t
        SITE-A(config)# router ospf 1
        SITE-A(config)# net 192.168.10.0 0.0.0.255 area 100
        SITE-A(config)# net 10.0.90.0 0.0.0.255 area 100
    ```
  - Trên R2
  - ```
        SITE-B# conf t
        SITE-B(config)# router ospf 1
        SITE-B(config)# net 192.168.20.0 0.0.0.255 area 100
        SITE-B(config)# net 10.0.100.0 0.0.0.255 area 100
    ```
   - Trên R3
    - ```
        R3#conf t
        R3(config)# router ospf 1
        R3(config)# net 10.0.0.0 0.0.0.255 area 100
        R3(config)# net 10.0.10.0 0.0.0.255 area 100
      ```
    - Trên R4
    - ```
        R4#conf t
        R4(config)# router ospf 1
        R4(config)# net 10.0.0.0 0.0.0.255 area 100
        R4(config)# net 10.0.20.0 0.0.0.255 area 100
        R4(config)# net 10.0.40.0 0.0.0.255 area 100
        R4(config)# net 10.0.90.0 0.0.0.255 area 100
      ```
    - Trên R5
    - ```
        R5#conf t
        R5(config)# router ospf 1
        R5(config)# net 10.0.10.0 0.0.0.255 area 100
        R5(config)# net 10.0.30.0 0.0.0.255 area 100
        R5(config)# net 10.0.50.0 0.0.0.255 area 100
        R5(config)# net 10.0.100.0 0.0.0.255 area 100
      ```
    - Trên R6
    - ```
        R6#conf t
        R6(config)# router ospf 1
        R6(config)# net 10.0.20.0 0.0.0.255 area 100
        R6(config)# net 10.0.30.0 0.0.0.255 area 100
        R6(config)# net 10.0.60.0 0.0.0.255 area 100
        R6(config)# net 10.0.70.0 0.0.0.255 area 100
      ```
    - Trên R7
    - ```
        R7#conf t
        R7(config)# router ospf 1
        R7(config)# net 10.0.40.0 0.0.0.255 area 100
        R7(config)# net 10.0.60.0 0.0.0.255 area 100
        R7(config)# net 10.0.80.0 0.0.0.255 area 100
      ```
    - Trên R8
    - ```
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
