# Step 2: Deploy ACR in a Private Network Environment

In this step, you deploy Azure Container Registry (ACR) to an existing Azure virtual network.

The ACR deployed in this step can be managed only through the Jump Box in the virtual network.

\[**Steps**▶️\]

1. Sign in to the [Azure portal](https://portal.azure.com), and select the \[**+**\] Create a resource icon at the top of the portal. If the icon is not displayed, select the hamburger menu in the upper-left corner, and then select \[**Create a resource**\].

    ![Create a resource](img/EN-create_AzureResource.png)

2. On the \[**Create a resource**\] page, enter `Container Registry` in the search box, and then select the \[**Container Registry**\] tile in the search results.

    ![Search for Azure Container Registry](img/EN-ACR_tail.png)

3. On the \[**Container Registry**\] page, select \[**Create**\].

4. On the \[**Basics**\] tab of the \[**Create container registry**\] page, configure each setting as follows.

    **Project details**

    |Setting|Value|
    |:---|:---|
    |Subscription \*|The subscription to use|
    |Resource group|\[*Any resource group*\]|

    **Instance details**

    |Setting|Value|
    |:---|:---|
    |Registry name \*|`pocacrpriv\<unique-value\>`|
    |Location \*|\[**(Asia Pacific) Japan West**\]|
    |Domain name label scope \*|\[**Unsecure**\]|
    |Availability zones|Not selected|
    |Pricing plan \*|\[**Premium**\] (Note 1)|
    |Role assignment permissions mode|Select \[**RBAC Registry Permissions**\] (Note 2)|

    ![Configure the Basics tab for ACR](img/EN-create-ACR-basic.png)

    (Note 1) The Premium plan is required to expose a private endpoint within a virtual network.

    (Note 2) `RBAC Registry + ABAC Repository Permissions` is expected to become the default in the future. This guide selects `RBAC Registry Permissions` to simplify the learning exercise. For production environments, select the configuration that best meets your requirements.

    After configuring the settings, select \[**Next: Networking**\] at the bottom of the page.

5. On the **Networking** page, under \[**Connectivity configuration**\], select \[**Private access (recommended)**\], and then select \[**+ Create a private endpoint**\].

6. The \[**Create private endpoint**\] pane appears on the right. Configure each setting as follows.

    |Setting|Value|
    |:---|:---|
    |Subscription \*|The subscription to use|
    |Resource group|\[*Any resource group*\]|
    |Location \*|\[**(Asia Pacific) Japan West**\]|
    |Name \*|`acr-privateLink`|
    |Registry subresource \*|registry|

    **Virtual network**

    |Setting|Value|
    |:---|:---|
    |Virtual network|\[**PoC-jpwest-vnet**\]|
    |Subnet \*|\[**poc-acr-subnet**\]|

    **Private DNS integration**

    |Setting|Value|
    |:---|:---|
    |Integrate with private DNS zone|\[**Yes**\]|
    |Private DNS zone \*|*Keep the default value*|

    After configuring the settings, select \[**OK**\].

7. Back on the **Create container registry** page, leave the other settings at their defaults, and select \[**Review + create**\] at the bottom of the page.

    ![Configure network settings for the container registry](img/EN-ACR-networkSettings.png)

    When the \[**Create**\] button appears, select it to begin deploying ACR.

You have now deployed Azure Container Registry (ACR) in the private network environment. Deployment may take several minutes. Wait for it to complete before proceeding to the next step.

<br>

## Next

👉 [Step 3: Sign in to ACR and Push an Image from the Jump Box](en-ex03.md)

👈 [Step 1: Create a Subnet for ACR in an Existing Azure Virtual Network](en-ex01.md)

---

🏚️ [Back to README](README.md)
