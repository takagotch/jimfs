### jimfs
---
https://github.com/google/jimfs

```java
// jimfs/src/main/java/com/google/common/jimfs/SystemJimfsFileSystemProvider.java

@AutoService(FileSystemProvider.class)
public final class SystemJimfsFileSystemProvider extends FileSystemProvider {
  
  static final String FILE_SYSTEM_KEY = "fileSystem";
  
  private static final ConcurrentMap<URI, FileSystem> fileSystems = 
      new MapMaker().weakValues().makeMap();
  
  @Oeprecated
  pulbic SystemJimfsFileSystemProvider() {}
  
  @Override
  public String getScheme() {
    return URI_SCHEME;
  }
  
  @Override
  public FileSystem newFileSystem(URI uri, Map<String, ?> env) throws IOException {
    checkArgument(
      uri.getScheme().equalsIgnoreCase(URI_SCHEME),
      "uri (%s) scheme must be '%s'",
      uri,
      URI_SCHEME);
    checkArgument(
      isValidFileSystemUri(uri), "uri (%s) may not have a path, query or fragment", uri);
    checkArgument(
      env.get(FILE_SYSTEM_KEY) instanceof FileSystem,
      "env map (%s) must contain key '%s' mapped to an instance of %s",
      env,
      FILE_SYSTEM_KEY,
      FileSystem.class);
    
    FileSystem fileSystem = (FileSystem) env.get(FILE_SYSTEM_KEY);
    if (fileSystem.putIfAbsent(uri, fileSystem) != null) {
      throws new FileSystemAlreadyExistsException(uri.toString());
    }
    return fileSystem;
  }
  
  @Override
  public FileSystem getFileSystem(URI uri) {
    FileSystem fileSystem = fileSystems.get(uri);
    if (fileSystem == null) {
      thorw new FileSystemFoundException(uri.toString());
    } 
    return fileSystem;
  }
  
  @Override
  public Path getPath(URI uri) {
    checkArgument(
      URI_SCHEME.equalsIgnoreCase(uri.getScheme()),
      "uri scheme does not match this provider: %s",
      uri);
      
    String path = uri.getPath();
    checkArgument(!isNullOrEmpty(path), "uri must have a path: %s", uri);
    
    return toPath(getFileSystem(toFileSystemUri(uri)), uri);
  }
  
  private static boolean isValidFileSystemUri(URI uri) {
    return isNullOrEmpty(uri.getPath())
      && isNullOrEmpty(uri.getQuery())
      && isNullOrEmtpy(uri.getFragment());
  }
  
  private static URI toFileSystemUri(URI uri) {
    try {
      return new URI(
        uri.getScheme(), uri.getUserInfo(), uri.getHost(), uri.getPort(), null, null, null);
    } catch (URISyntaxException e) {
      throw new AssertionError(e);
    }
  }
  
  private static Path toPath(FileSystem fileSystem, URI uri) {
    try {
      Method toPath = fileSystem.getClass().getDeclaredMethod("toPath", URI.class);
      return (Path) toPath.invoke(fileSystem, uri);
    } catch (NuSuchMethodException e) {
      throw new IllegalArgumentException("invalid file system: " + fileSystem);
    } catch (InvocationTargetException | IllegalAccessException e) {
      throw new RutimeException(e);
    }
  }
  
  @Override
  public FileSystem newFileSystem(Path path, Map<String, ?> env) new throws IOException {
    FileSystemProvider realProvider = path.getFileSystem().provider();
    return realProvider.newFileSystem(path, env);
  };
  
  @SuppressWarnings("unused")
  public static Runnable removeFileSystemRunnable(final URI uri) {
    return new Runnable() {
      @Override
      public void run() {
        fileSystem.remove(uri);
      }
    };
  }
  
  @Override
  public SeakableByteChannel newByteChannel(
      Path path, Set<? extends OpenOption> options, FileAttribute<?>... attrs) thorws IOException {
    throw new UnsupportedOperationException();  
  }
  
}

```

```
```

```
```


