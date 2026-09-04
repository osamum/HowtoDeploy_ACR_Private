# 手順 3: Jump Box から ACR へのサインインとイメージの Push

閉域化環境にデプロイされた Azure Container Registry (ACR) に同じ仮想ネットワーク上にある Jump Box マシンから接続します。

この作業を実施する前に Jump Box 側での準備が必要になります。

## 準備

ACR にサインインするためのコマンド `az acr login`を実行するには、実行するマシン上 Docker CLI と Docker daemon の両方が必要になるため Jump Box マシンに Docker のインストールが必要になります。

Docker Desktop のライセンスの有償、無償の条件を確認し、使用可能である場合は Jump Box マシンに [Docker Desktop](https://docs.docker.com/desktop/setup/install/windows-install/) をインストールして使用することができるので、リンク先の案内に従って[インストール](https://docs.docker.com/desktop/setup/install/windows-install/#install-docker-desktop-on-windows)を行い、Docker サービスを起動してください。

Docker Desktop が使用できる場合は、次の手順はスキップしてください。

### Docker Desktop が使用できない場合

ライセンス等の関係で Docker Desktop が使用できない場合は、Windows Subsystem for Linux (WSL) をインストールし、そこに Docker Engine をインストールして Docker daemon を動かします。 

具体的な手順は以下のとおりです。

\[**手順**▶️\]

1. Bastion を使用して Jump Box マシンにログインし、ターミナル画面を起動します

2. 以下のコマンドを実行し、[入れ子になった仮想化(Nested-Virtualization)](https://learn.microsoft.com/windows-server/virtualization/hyper-v/enable-nested-virtualization) が使用できるか確認します

    ```
    Get-ComputerInfo -Property HyperV*
    ```
    結果に `HyperVisorPresent : True` と返れば Hyper-Visor が使用できるので問題ありません。もし、False の場合はここで作業を中断し、Jump Box の仮想マシンのサイズを `Standard D2lds v5` などに変更してください。
   
3. WSL コマンドをインストールします
   
   ```
   wsl --install
   ```

4. Linux ディストリビューションとして Ubuntu をインストールします

    ```
    wsl --install -d Ubuntu
    ```
    
    インストール時に、管理者パスワードを求められるので設定してください。

    インストールが完了したらターミナル ウィンドウを閉じて、再度起動します。

5. 以下のコマンドを実行してプロンプトが切り替わることを確認します

    ```
    bash
    ```
    
    もし切り替わらない場合は、Jump Box マシンを再起動します

6. ターミナル画面が bash で動作している状態で、WSL 側に Azure CLI をインストールします

    以下の[コマンド](https://learn.microsoft.com/cli/azure/install-azure-cli-linux?view=azure-cli-latest&pivots=apt#option-1-install-with-one-command)を実行します。

    ```
    curl -fsSL 'https://azurecliprod.blob.core.windows.net/$root/deb_install.sh' | sudo bash
    ```
    インストールが完了したらターミナル画面を再起動してプロンプトを Bash に切り替え、以下のコマンドを実行します。

    ```
    which az
    ```

    `/usr/bin/az` と返ることを確認します。

7. WSL に Docker Engine をインストールします

    以下のドキュメントの内容に従いインストールを行ってください。

    * [**Install Docker Engine on Ubuntu - Install using the repositoryapt**](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

    続けて、以下のドキュメントの**ステップ 3 まで**を実行します。

    * [**Manage Docker as a non-root user**](https://docs.docker.com/engine/install/linux-postinstall/)
    
8. ターミナル画面のプロンプトが Bash であることを確認し、以下のコマンドで Azure にログインします

    ```
    az login
    ```
    
    正常にログインできることを確認します。

ここまでの手順で、Azure Container Registry (ACR) にログインする準備ができました。

<br>

## ACR にログインしてイメージを push する

Jump Box のターミナル画面から Azure Container Registry (ACR) にログインし、サンプル アプリケーション


https://learn.microsoft.com/ja-jp/azure/container-registry/container-registry-get-started-portal?tabs=azure-cli