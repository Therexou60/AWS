# Implementing VPC Peering on AWS

## Introduction

In this live environment, you will 
learn how to create, and configure VPC peering within AWS. VPC peering 
is a feature of AWS which allows cross-VPC communication, without 
additional hardware, or software solutions. VPC peering is a feature you
 will use daily in production environments, and it's useful to know for 
all of the AWS exams.

The environment is split into three 
stages. There is an architectural overview first, followed by the 
creation and configuration of a VPC peer, and finally the live 
environment will finish by demonstrating the limitations of VPC peering 
and some advanced features.

By the end of the lab, you will be 
able to comfortably implement VPC peering, know it's limitations, and 
perhaps more importantly understand when and why you would use the 
feature.

## Solution

Log in to the AWS Management Console using the credentials provided on the lab instructions page. Make sure you're using the *us-east-1* region.

---

Log in to the servers using the credentials provided:

```
ssh cloud_user@<PUBLIC_IP_ADDRESS>

```

### Create a VPC Peer

1. In `INSTANCE1`, ping the public IP address for `INSTANCE2`:
    
    ```
    ping <INSTANCE2_PUBLIC_IP_ADDRESS>
    
    ```
    
2. In the AWS Console, navigate to the **VPC Dashboard**.
3. Click **Network ACLs**.
4. Select **Public2-NACL**.
5. In the tab at the bottom of the page, select **Inbound Rules**.
6. Click **Edit**.
7. For rule `104`, enter the following in the *Source* field:
    
    ```
    10.0.0.0/13
    
    ```
    
8. Click **Save**.
9. In `INSTANCE1`, ping the public IP address for `INSTANCE2` again:
    
    ```
    ping <INSTANCE2_PUBLIC_IP_ADDRESS>
    
    ```
    
10. In the AWS Console, navigate back to the **VPC Dashboard**.
11. Under, *Virtual Private Cloud*, click **Peering Connections**.
12. Click **Create Peering Connection**.
13. For *VPC (Requester)*, select `VPC1`.
14. For *VPC (Accepter)*, select `VPC2`.
15. Click **Create Peering Connection**.
16. Click **OK**.
17. At the top of the page, click **Actions**, and select **Accept Request**.
18. Back in `INSTANCE1`, ping the public IP address for `INSTANCE2`:
    
    ```
    ping <INSTANCE2_PUBLIC_IP_ADDRESS>
    
    ```
    

### Configure Routing

1. In the **VPC Dashboard**, click **Your VPCs**, and copy the `IPv4 CIDR` for `VPC1`, `VPC2`, and `VPC3`.
2. Click **Route Tables**, and select `Public1-RT`.
3. At the bottom of the page, select the **Routes** tab, and click **Edit**.
4. Click **Add another route**.
5. For *Destination*, enter the `<VPC2_IPV4_CIDR>`, and in the *Target* box, select the target starting with `pcx`.
6. Click **Save**.
7. Repeat these steps for `Private1-RT`.
8. Click `Public2-RT`.
9. At the bottom of the page, select the **Routes** tab, and click **Edit**.
10. Click **Add another route**.
11. For *Destination*, enter the `<VPC1_IPV4_CIDR>`, and in the *Target* box, select the target starting with `pcx`.
12. Click **Save**.
13. Repeat these steps for `Private2-RT`.
14. Back in `INSTANCE1`, ping the public IP address for `INSTANCE2` again:
    
    ```
    ping <INSTANCE2_PUBLIC_IP_ADDRESS>
    
    ```
    

### Create VPC Peer Mesh

1. In the **VPC Dashboard**, under, *Virtual Private Cloud*, click **Peering Connections**.
2. Click **Create Peering Connection**.
3. For *VPC (Requester)*, select `VPC2`.
4. For *VPC (Accepter)*, select `VPC3`.
5. Click **Create Peering Connection**.
6. Select the pending connection.
7. At the top of the page, click **Actions**, and select **Accept Request**.
8. Copy the `Peering Connection Id` for the new connection.
9. In **Route Tables**, and select `Public2-RT`.
10. At the bottom of the page, select the **Routes** tab, and click **Edit**.
11. Click **Add another route**.
12. For *Destination*, enter the `<VPC3_IPV4_CIDR>`, and in the *Target* box, paste the `<PEERING_CONNECTION_ID>`.
13. Click **Save**.
14. Repeat these steps for `Private2-RT`.
15. Select `Public3-RT`.
16. At the bottom of the page, select the **Routes** tab, and click **Edit**.
17. Click **Add another route**.
18. For *Destination*, enter the `<VPC2_IPV4_CIDR>`, and in the *Target* box, paste the `<PEERING_CONNECTION_ID>`.
19. Click **Save**.
20. Repeat these steps for `Private3-RT`.
21. Back in `INSTANCE1`, ping the public IP address for `INSTANCE3` again:
    
    ```
    ping <INSTANCE3_PUBLIC_IP_ADDRESS>
    
    ```
    
22. In the AWS Console, navigate to the **VPC Dashboard**.
23. Click **Network ACLs**.
24. Select **Private3-NACL**.
25. In the tab at the bottom of the page, select **Inbound Rules**.
26. Click **Edit**.
27. Click **Add another rule**, and enter the following:
    - *Rule #:* **100**
    - *Type:* **All ICMP**
    - *Source:* **10.0.0.0/13**
28. Click **Save**.
29. Click the **Outbound Rules** tab, and repeat these steps.
30. In `INSTANCE1`, ping the public IP address for `INSTANCE3` again:
    
    ```
    ping <INSTANCE3_PUBLIC_IP_ADDRESS>
    
    ```
    
31. In the **VPC Dashboard**, under, *Virtual Private Cloud*, click **Peering Connections**.
32. Click **Create Peering Connection**.
33. For *VPC (Requester)*, select `VPC1`.
34. For *VPC (Accepter)*, select `VPC3`.
35. Click **Create Peering Connection**.
36. Select the pending connection.
37. At the top of the page, click **Actions**, and select **Accept Request**.
38. Copy the `Peering Connection Id` for the third connection.
39. In **Route Tables**, and select `Public1-RT`.
40. At the bottom of the page, select the **Routes** tab, and click **Edit**.
41. Click **Add another route**.
42. For *Destination*, enter the `<VPC3_IPV4_CIDR>`, and in the *Target* box, paste the `<PEERING_CONNECTION_ID>` for the third connection.
43. Click **Save**.
44. Repeat these steps for `Private1-RT`.
45. Select `Public3-RT`.
46. At the bottom of the page, select the **Routes** tab, and click **Edit**.
47. Click **Add another route**.
48. For *Destination*, enter the `<VPC1_IPV4_CIDR>`, and in the *Target* box, paste the `<PEERING_CONNECTION_ID>`.
49. Click **Save**.
50. Repeat these steps for `Private3-RT`.
51. In the `INSTANCE1` terminal, ping the public IP address for `INSTANCE3` again:
    
    ```
    ping <INSTANCE3_PUBLIC_IP_ADDRESS>
    
    ```
    

### DNS Over VPC Peer

1. In the AWS Console, navigate to the **EC2 Dashboard**.
2. Select `Instance2`.
3. Copy the `Public DNS (IPv4)` URL.
4. In the `INSTANCE1` terminal, ping the `Instance2` URL:
    
    ```
    ping <INSTANCE2_PUBLIC_DNS>
    
    ```
    
5. In the VPC Dashboard, click **Peering Connections**.
6. Select the connection that connects `VPC1` and `VPC2`, and in the *Actions* menu, click **Edit DNS Settings**.
7. Check both boxes under *DNS Resolution*, and click **Save**.
8. In the `INSTANCE1` terminal, ping the `Instance2` URL:
    
    ```
    ping <INSTANCE2_PUBLIC_DNS>
    
    ```
    

## Conclusion

Congratulations — you've completed this hands-on lab!