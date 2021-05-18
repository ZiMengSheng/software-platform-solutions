# Download and install

## 1. Go download.

Click the button below to download the Go installer.

[**Download Go for Linux**](https://golang.org/dl/go1.16.3.linux-amd64.tar.gz)

Don't see your operating system here? Try one of the [other downloads](https://golang.org/dl/).

**Note:** By default, the `go` command downloads and authenticates modules using the Go module mirror and Go checksum database run by Google. [Learn more.](https://golang.org/dl)

## 2. Go install.

Select the tab for your computer's operating system below, then follow its installation instructions.

### Linux

1. Extract the archive you downloaded into /usr/local, creating a Go tree in /usr/local/go.

   **Important:** This step will remove a previous installation at /usr/local/go, if any, prior to extracting. Please back up any data before proceeding.

   For example, run the following as root or through `sudo`:

   ```
   rm -rf /usr/local/go && tar -C /usr/local -xzf go1.16.3.linux-amd64.tar.gz
   ```

2. Add /usr/local/go/bin to the`PATH`environment variable.

   You can do this by adding the following line to your $HOME/.profile or /etc/profile (for a system-wide installation):

   ```
   export PATH=$PATH:/usr/local/go/bin
   ```

   **Note:** Changes made to a profile file may not apply until the next time you log into your computer. To apply the changes immediately, just run the shell commands directly or execute them from the profile using a command such as `source $HOME/.profile`.

3. Verify that you've installed Go by opening a command prompt and typing the following command:

   ```
   $ go version
   ```

4. Confirm that the command prints the installed version of Go.

## 3. ENV

添加环境变量

```
GOROOT=/usr/local/go
PATH=$PATH:$GOROOT/bin
GOPATH=/home/admin/go
PATH=$PATH:$GOPATH/bin
```

```go
go env -w GOPROXY=https://goproxy.cn,direct
go env -w GO111MODULE=on
```