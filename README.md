# java-gui

vscode/container first approach to building/deploying javafx apps

## .devcontainer

Uses containers to build/deploy the javafx code

######vscode devcontainer based
https://code.visualstudio.com/docs/remote/containers

######oraclelinux:8-slim based
https://hub.docker.com/_/oraclelinux




## useful snippets to test stuff out

    local> docker run -i -t --rm oraclelinux:7-slim
    dc> cat /etc/redhat-release


## setup Windows HOST with JavaFX & JavaFXMods

1. download javafx/javafx mods version 13 to your host from here : https://gluonhq.com/products/javafx/

2. setup env
    - PATH_TO_FX = E:\javafx-sdk-13.0.2\lib
    - PATH_TO_FX_MODS = E:\javafx-jmods-13.0.2

[note: this means you can run the exact same code in windows as in linux container]