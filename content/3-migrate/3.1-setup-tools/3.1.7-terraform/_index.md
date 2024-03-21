---
title : "Install Terraform tool"
date : "`r Sys.Date()`"
weight : 7
chapter : false
pre : " <b> 3.1.7 </b> "
---
**_Refer: https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli_**

**Step 1:** Ensure that your system is up to date and you have installed: 
  - installed the gnupg
  - software-properties-common
  - curl packages

You will use these packages to verify HashiCorp's GPG signature and install HashiCorp's Debian package repository.

        sudo apt-get update -y && sudo apt-get install -y gnupg software-properties-common

**Step 2:** Install the HashiCorp GPG key.

    wget -O- https://apt.releases.hashicorp.com/gpg | \
    gpg --dearmor | \
    sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null


**Step 3:** Verify the key's fingerprint.

    gpg --no-default-keyring \
    --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
    --fingerprint
        The gpg command will report the key fingerprint:
        /usr/share/keyrings/hashicorp-archive-keyring.gpg
        -------------------------------------------------
        pub   rsa4096 XXXX-XX-XX [SC]
        AAAA AAAA AAAA AAAA
        uid           [ unknown] HashiCorp Security (HashiCorp Package Signing) <security+packaging@hashicorp.com>
        sub   rsa4096 XXXX-XX-XX [E]

**Step 4:** Add the official HashiCorp repository to your system. 

The lsb_release -cs command finds the distribution release codename for your current system, such as buster, groovy, or sid.

    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
    https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
    sudo tee /etc/apt/sources.list.d/hashicorp.list

**Step 5:** Download the package information from HashiCorp.

    sudo apt update -y

**Step 6:** Install Terraform from the new repository.

    sudo apt-get install terraform -y

*** Verify the installation

    terraform --help
*** Add any subcommand to terraform -help to learn more about what it does and available options.

    terraform --help plan
