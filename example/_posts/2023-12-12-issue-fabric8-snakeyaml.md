---
layout: post
title: io.fabric8.kubernetes.client.utils.Serialization.unmarshal
description: >
  fix to issue of `io.fabric8.kubernetes.client.utils.Serialization.unmarshal`
sitemap: false
hide_last_modified: true
categories: [issue, kubernetes, fabric8, snakeyaml, kubectl, java]
tags:       [issue, kubernetes, fabric8, snakeyaml, kubectl, java]
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Recommended Specifications

- jdk 17
- snakeyaml 2.x
- kubernetes-client.version 6.9.x

## Reference site

- [fabric8 io githup]{:.heading.flip-title}  &rarr; fabric8 io githup tree
{:.related-posts.faded}

## Get kube_config.yaml

- before

```java
// file: `GetKubernetesClient.java`
import io.fabric8.kubernetes.client.Config;
import io.fabric8.kubernetes.client.KubernetesClient;
import io.fabric8.kubernetes.client.KubernetesClientBuilder;

public class GetKubernetesClient {
    void exampleGetKubernetesClient() {
        System.setProperty("kubeconfig", kubeConfigPath);

        try ( KubernetesClient kubernetesClient = new DefaultKubernetesClient() ) {

            ...
            ...
        }
    }
}
```

- fix

```java
// file: `GetKubernetesClient.java`
import io.fabric8.kubernetes.client.Config;
import io.fabric8.kubernetes.client.KubernetesClient;
import io.fabric8.kubernetes.client.KubernetesClientBuilder;

public class GetKubernetesClient {
    void exampleGetKubernetesClient() {
        KubeConfigService kubeConfigService = new KubeConfigService();	
        Config config = kubeConfigService.getConfig(); 
        try (KubernetesClient kubernetesClient = new KubernetesClientBuilder().withConfig(config).build() ) {
            ...
            ...
        }
    }
}
```

```java
// file: `KubeConfigService.java`
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.Properties;

import org.springframework.util.ResourceUtils;

import io.fabric8.kubernetes.client.Config;

public class KubeConfigService {

    public KubeConfigService() { }
    
    public Config getConfig() {
      String kubeconfigContents;
      Config config = null;
      
      kubeconfigContents = this.getKubeconfigContents();

      config = Config.autoConfigure(kubeconfigContents);
      
        return config;
    }

    private String getKubeconfigContents() {
      
      File file = null;
      String kubeconfigContents = null;
      Properties properties = new Properties();
      try {
        file = ResourceUtils.getFile("classpath:application.yml");
        
        try( InputStream inputStream = new FileInputStream(file) ) {
          properties.load(inputStream);
          
          String filepath = properties.getProperty("kubeconfig");
          
          Path path = Paths.get(filepath);
          kubeconfigContents = String.join("\n", Files.readAllLines(path));
          
        } catch (IOException ie) {
                  ie.printStackTrace();
        } 
        
      } catch (FileNotFoundException fnfe) {
              fnfe.printStackTrace();
      } 

      return kubeconfigContents;
    }
}
```

{:.related-posts.faded}

## Create kubernetes object

```java
// before
// error because snakeyaml.version 2.x
Service service = client.services().load( <ios> ).get();

// after
// fix
ObjectMapper objectMapper = new ObjectMapper( new YAMLFactory() );
Service service = objectMapper.readValue( <byte_array>, Service.class);
```

{:.related-posts.faded}


[fabric8 io githup]: https://github.com/fabric8io/kubernetes-client/tree/main
