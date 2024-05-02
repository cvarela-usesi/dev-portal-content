---
title: "Updating VTEX IO's CLI"
slug: "vtex-io-documentation-vtex-io-cli-update"
hidden: false
createdAt: "2021-10-20T14:59:19.840Z"
updatedAt: "2022-12-13T20:17:44.315Z"
---
If the VTEX IO’s CLI version installed on your machine is outdated or deprecated, run the command relative to your operating system to update it.

<details>
  <summary><span class="fa fa-apple">&nbsp;</span>MacOS</summary>
  <br>
  
- Brew

```sh Update
brew upgrade vtex
```

```sh Reinstall
brew unlink vtex
brew install vtex/vtex
```

<br>
</details>

<details>
  <summary><span class="fa fa-linux">&nbsp;</span>Linux</summary>
<br>

- Standalone
  
```sh
vtex autoupdate
```  

> ℹ️ The standalone update is a tarball with a binary that contains its own node.js binary.

<br>
</details>

<details>
  <summary><span class="fa fa-windows">&nbsp;</span>Windows</summary>
<br>

- Standalone.exe

```sh
vtex autoupdate
```

<br>
</details>

## Updating VTEX IO's CLI via NPM

If you have [installed VTEX IO's CLI via NPM](https://developers.vtex.com/docs/guides/vtex-io-documentation-vtex-io-cli-install), run the following command to update or handle a deprecated version:

```sh
yarn global add vtex
```
