**Generate .java protobuf files from .proto sources**

_1. Install **protoc** before you go next steps_

```bash
    sudo apt-get install libprotoc
```
    
_2. Create in src/main/ **'protos'** package, then place .proto source files there_

_3. Add gradle task:_

```java
    task task_name() {
        generate(java_package, regenerate)
    }
    preBuild.dependsOn task_name
```
    
_4. Add next lines to gradle.build:_

```java
    public void generate(String java_package, boolean regenerate) {
    
        final String project_path = project.projectDir.path + "/src/main"
    
        String proto_path = project_path + "/protos",
                java_out = project_path + "/java",
                proto_out = java_out + java_package
    
        String ls_result = execute_cmd("ls " + proto_path)
    
        String[] proto_files = ls_result.split("\n")
    
        File proto_dir = new File(proto_out)
    
        if (!proto_dir.exists()) {
            for(String file : proto_files) {
                execute_cmd("protoc --proto_path=/" + proto_path +
                        " --java_out=" + java_out +
                        " " + proto_path +
                        "/" + file)
            }
    
        } else {
            if (regenerate) {
                for(String file : proto_files) {
                    execute_cmd("protoc --proto_path=/" + proto_path +
                            " --java_out=" + java_out +
                            " " + proto_path +
                            "/" + file)
                }
            }
        }
    
    
    }
```

_... and this too._

```java
    public String execute_cmd(String command) {
    
        StringBuffer output = new StringBuffer();
    
        Process p;
        try {
            p = Runtime.getRuntime().exec(command);
            p.waitFor();
            BufferedReader reader =
                    new BufferedReader(new InputStreamReader(p.getInputStream()));
    
            String line = "";
            while ((line = reader.readLine())!= null) {
                output.append(line + "\n");
            }
    
        } catch (Exception e) {
            e.printStackTrace();
        }
    
        return output.toString();
    
    }
```