
从 Dubbo.zookeeper 注册地址提取 zookeeper 地址

```java
import java.util.Optional;

public class ZookeeperURL {
    public static final String PREFIX = "zookeeper://";
    public static final String BACKUP = "backup=";

    public static Optional<String> convertDubboRegistryToZookeeperURL(String dubboRegistry){
        StringBuilder zookeeperURL = new StringBuilder();
        if(dubboRegistry != null && dubboRegistry.startsWith(PREFIX)){
            dubboRegistry = dubboRegistry.substring(PREFIX.length());
            int index = dubboRegistry.indexOf("?");
            if(index > 0){
                zookeeperURL.append(dubboRegistry.substring(0, index));
                dubboRegistry = dubboRegistry.substring(index + 1);
                String[] dubboRegistries = dubboRegistry.split("&");
                for (int i = 0; i < dubboRegistries.length; i++) {
                    if(dubboRegistries[i].startsWith(BACKUP)){
                        String[] backups = dubboRegistries[i].substring(BACKUP.length()).split(",");
                        for (int j = 0; j < backups.length; j++) {
                            zookeeperURL.append(",").append(backups[i]);
                        }
                    }
                }
            } else {
                zookeeperURL.append(dubboRegistry);
            }
            return Optional.of(zookeeperURL.toString());
        }
        return Optional.empty();
    }

    public static void main(String[] args) {
        System.out.println(convertDubboRegistryToZookeeperURL("zookeeper://localhost:2181"));
        System.out.println(convertDubboRegistryToZookeeperURL("zookeeper://localhost:2181?client=zkclient"));
        System.out.println(convertDubboRegistryToZookeeperURL("zookeeper://localhost:2181?backup=localhost:2182,localhost:2183"));
        System.out.println(convertDubboRegistryToZookeeperURL("zookeeper://localhost:2181?client=zkclient&backup=localhost:2182,localhost:2183"));
    }
}
```

