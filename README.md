## 本库基于`Android、iOS` <https://github.com/philipphecht/react-native-doc-viewer>扩展而来

## **_这是一款基于 React Native HarmonyOS 端文件文档查看器（pdf、png、jpg、xml、xls、doc、ppt、xlsx、docx、pptx 等）开源插件_**

> ### 版本：latest

<p align="center">
  <h1 align="center"> <code>react-native-ohos-docviewer</code> </h1>
</p>
<p align="center">
    <a href="https://github.com/wonday/react-native-pdf/blob/master/LICENSE">
        <img src="https://img.shields.io/badge/license-MIT-green.svg" alt="License" />
    </a>
</p>

> [!TIP] [Github 地址](https://github.com/yrjwcharm/react-native-ohos-docviewer)

## 安装与使用

#### **npm**

```bash
npm install react-native-ohos-docviewer
```

#### **yarn**

```bash
yarn add react-native-ohos-docviewer
```

下面的代码展示了这个库的基本使用场景：基于

> [!WARNING] 使用时 import 导入的库名不变。因为该库具有 alias 别名: react-native-ohos-docviewer [主要是统一 android/ios import 导入]

```js
import React, { useEffect } from "react";
import {
  Button,
  DeviceEventEmitter,
  SafeAreaView,
  StatusBar,
  StyleSheet,
} from "react-native";
import OpenFile from "react-native-doc-viewer";
import { getBase64ImagePath } from "./imgbase64";
const App = () => {
  useEffect(() => {
    //监听文件预览下载进度
    DeviceEventEmitter.addListener("RNDownloaderProgress", (event) => {
      // 添加事件处理
      console.log("Download progress:", event.progress);
    });
    return () => {
      // 清理事件监听器
      DeviceEventEmitter.removeAllListeners("RNDownloaderProgress");
    };
  }, []);
  const previewImage = () => {
    OpenFile.openDoc(
      [
        {
          url: "https://i.gsxcdn.com/2015162519_i828z3ug.jpeg",
        },
      ],
      (error: any, url: string) => {
        if (error) {
        } else {
          console.log(url);
        }
      }
    );
  };
  const previewWord = () => {
    OpenFile.openDoc(
      [
        {
          url: "https://calibre-ebook.com/downloads/demos/demo.docx",
        },
      ],
      (error: any, url: string) => {
        if (error) {
        } else {
          console.log(url);
        }
      }
    );
  };
  const previewBase64 = () => {
    OpenFile.openDocb64(
      [
        {
          base64: getBase64ImagePath(),
          fileName: "example",
          fileType: "jpg",
        },
      ],
      (error: any, url: string) => {
        if (error) {
        } else {
          console.log(url);
        }
      }
    );
  };
  const previewXML = () => {
    OpenFile.openDocBinaryinUrl(
      [
        {
          url: "https://storage.googleapis.com/need-sure/example",
          fileName: "example",
          fileType: "xml",
        },
      ],
      (error: any, url: string) => {
        if (error) {
          console.log("Error opening XML file:", error);
        } else {
          console.log(url);
        }
      }
    );
  };
  return (
    <SafeAreaView style={styles.container}>
      <StatusBar barStyle={"dark-content"} />
      <Button onPress={previewImage} title="预览图片" />
      <Button onPress={previewWord} title="预览word文档" />
      <Button onPress={previewBase64} title="base64打开预览" />
      <Button onPress={previewXML} title="预览XML" />
    </SafeAreaView>
  );
};
export default App;
const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
});
```

## Link

目前 HarmonyOS 暂不支持 AutoLink，所以 Link 步骤需要手动配置。

首先需要使用 DevEco Studio 打开项目里的 HarmonyOS 工程 `harmony`

### 1.在工程根目录的 `oh-package.json5` 添加 overrides 字段

```json
{
  ...
  "overrides": {
    "@rnoh/react-native-openharmony": "file:../libs/react_native_openharmony_release.har"

  }
}
```

### 2.引入原生端代码 ，目前有两种方法：

    * 1. 通过 har 包引入（在 IDE 完善相关功能后该方法会被遗弃，目前首选此方法）；
    * 2. 直接链接源码。

方法一：通过 har 包引入（推荐）

> [!TIP] har 包位于三方库安装路径的 `harmony` 文件夹下。

打开 `entry/oh-package.json5`，添加以下依赖

```json
"dependencies": {
    "@rnoh/react-native-openharmony": "file:../libs/react_native_openharmony_release.har"
    "@react-native-ohos/react-native-doc-viewer": "file:../../node_modules/react-native-ohos-docviewer/harmony/docviewer.har",
  },
```

点击右上角的 `sync` 按钮

或者在终端执行：

```bash
cd entry
ohpm install

```

方法二：直接链接源码

> [!TIP] 从 react-native-ohos-docviewer 获取到 docviewer 源码文件，直接在 harmony 工程中通过 File->New->Import->Import Module 导入即可 主工程`entry/oh-package.json5`中添加

```json
"dependencies": {
    "@rnoh/react-native-openharmony": "file:../libs/react_native_openharmony_release.har"
    "@react-native-ohos/react-native-doc-viewer": "file:../docviewer",

  }
```

### 3.配置 CMakeLists 和引入 DocViewerPackage

打开 `entry/src/main/cpp/CMakeLists.txt`，添加：

```diff
project(rnapp)
cmake_minimum_required(VERSION 3.4.1)
set(CMAKE_SKIP_BUILD_RPATH TRUE)
set(RNOH_APP_DIR "${CMAKE_CURRENT_SOURCE_DIR}")
set(NODE_MODULES "${CMAKE_CURRENT_SOURCE_DIR}/../../../../../node_modules")
set(OH_MODULES "${CMAKE_CURRENT_SOURCE_DIR}/../../../oh_modules")
+set(OH_MODULE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../../../oh_modules")
set(RNOH_CPP_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../../../../../../react-native-harmony/harmony/cpp")
set(LOG_VERBOSITY_LEVEL 1)
set(CMAKE_ASM_FLAGS "-Wno-error=unused-command-line-argument -Qunused-arguments")
set(CMAKE_CXX_FLAGS "-fstack-protector-strong -Wl,-z,relro,-z,now,-z,noexecstack -s -fPIE -pie")
set(WITH_HITRACE_SYSTRACE 1) # for other CMakeLists.txt files to use
add_compile_definitions(WITH_HITRACE_SYSTRACE)

add_subdirectory("${RNOH_CPP_DIR}" ./rn)

# RNOH_BEGIN: manual_package_linking_1
add_subdirectory("../../../../sample_package/src/main/cpp" ./sample-package)
+ add_subdirectory("${OH_MODULE_DIR}/@react-native-ohos/react-native-doc-viewer/src/main/cpp" ./docviewer)

# RNOH_END: manual_package_linking_1

file(GLOB GENERATED_CPP_FILES "./generated/*.cpp")

add_library(rnoh_app SHARED
    ${GENERATED_CPP_FILES}
    "./PackageProvider.cpp"
    "${RNOH_CPP_DIR}/RNOHAppNapiBridge.cpp"
)
target_link_libraries(rnoh_app PUBLIC rnoh)

# RNOH_BEGIN: manual_package_linking_2
target_link_libraries(rnoh_app PUBLIC rnoh_sample_package)

+ target_link_libraries(rnoh_app PUBLIC rnoh_docviewer)

# RNOH_END: manual_package_linking_2
```

打开 `entry/src/main/cpp/PackageProvider.cpp`，添加：

```diff
#include "RNOH/PackageProvider.h"
#include "SamplePackage.h"
+ #include "DocViewerPackage.h"

using namespace rnoh;

std::vector<std::shared_ptr<Package>> PackageProvider::getPackages(Package::Context ctx) {
    return {
      std::make_shared<SamplePackage>(ctx),
+     std::make_shared<DocViewerPackage>(ctx),

    };
}
```

### 4.在 ArkTs 侧引入 DocViewerPackage

打开 `entry/src/main/ets/RNPackagesFactory.ts`，添加：

```diff
import type {RNPackageContext, RNPackage} from 'rnoh/ts';
import {SamplePackage} from 'rnoh-sample-package/ts';
+ import { DocViewerPackage } from '@react-native-ohos/react-native-doc-viewer';

export function createRNPackages(ctx: RNPackageContext): RNPackage[] {
  return [
    new SamplePackage(ctx),
+   new DocViewerPackage(ctx)

  ];
}
```

### 6.运行

点击右上角的 `sync` 按钮

或者在终端执行：

```bash
cd entry
ohpm install
```

然后编译、运行即可。

## API 文档

| API 方法               | 描述                                                               |
| ---------------------- | ------------------------------------------------------------------ |
| `openDoc`              | {url:string,fileName:string, cache:boolean}                        |
| `openDocb64`           | {url:string,fileName:string,fileType:string, cache:boolean }       |
| `openDocBinaryinUrl`   | {url:string,fileName:string,fileType:string }                      |
| `RNDownloaderProgress` | DeviceEventEmitter.addListener('RNDownloaderProgress',(event)=>{}) |

#### 开源不易，希望您可以动一动小手点点小 ⭐⭐

#### 👴 希望大家如有好的需求踊跃提交,如有问题请提交 issue，空闲时间会扩充与修复优化

## 开源协议

本项目基于 [The MIT License (MIT)](https://github.com/yrjwcharm/react-native-ohos-/blob/master/LICENSE) ，请自由地享受和参与开源。
