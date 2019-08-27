# Servlet 上传文件

html
```html
<body>
  <%--enctype属性规定在发送到服务器之前应该如何对表单数据进行编码。在使用包含文件上传控件的表单时，必须使用该值。--%>
  <form
    action="${pageContext.request.contextPath}/uploadFiles?method=uploadFiles"
    method="post"
    enctype="multipart/form-data"
  >
    <%-- 支持选取多个文件，如果需要上传文件夹需要添加webkitdirectory属性--%>
    <input type="file" name="file" multiple="multiple" />
    <input type="submit" value="submit" />
  </form>
</body>
```

servlet

```java
@WebServlet(urlPatterns = "/uploadFiles")
//处理文件上传的开关
@MultipartConfig
public class UploadFiles extends BaseServlet {
    public void uploadFiles(HttpServletRequest req, HttpServletResponse resp) throws IOException, ServletException {
        /*
        获取单个上传文件请求
        Part part = req.getPart("file");
         */
        Collection<Part> parts = req.getParts();
        //服务端保存文件的路径
        String path = req.getServletContext().getRealPath("/WEB-INF/files");
        for (Part part : parts) {
            /*
            通过req获得的请求头中不包含content-disposition。
            需要通过part获取文件名
             */
            Collection<String> headerNames = part.getHeaderNames();
            for (String headerName : headerNames) {
                System.out.println(headerName + " " + part.getHeader(headerName));
            }
            String str = part.getHeader("content-disposition");
            String fileName = str.substring(str.indexOf("filename") + 10, str.length() - 1);
            part.write(path + File.separator + fileName);
            /*
            Servlet3.1+tomcat8.0
            part.write(path+ File.separator+part.getSubmittedFileName());
             */
        }
    }
}
```

克隆地址：https://github.com/T00OM/Servlet3.0
