---
title: java使用zxing生成二维码
date: 2024-07-18 17:53:20
tags: java
---

### 导入依赖
```xml
<properties>
   <zxing.version>3.4.0</zxing.version>
</properties>
 <!--二维码依赖-->
 <dependency>
   <groupId>com.google.zxing</groupId>
   <artifactId>core</artifactId>
   <version>${zxing.version}</version>
 </dependency>
 <dependency>
   <groupId>com.google.zxing</groupId>
   <artifactId>javase</artifactId>
   <version>${zxing.version}</version>
 </dependency>
```
### 封装方法
```java
import com.google.zxing.*;  
import com.google.zxing.client.j2se.BufferedImageLuminanceSource;  
import com.google.zxing.common.BitMatrix;  
import com.google.zxing.common.HybridBinarizer;  
import com.google.zxing.qrcode.decoder.ErrorCorrectionLevel;  
import org.springframework.beans.factory.annotation.Value;  
import org.springframework.stereotype.Component;  
  
import javax.imageio.ImageIO;  
import java.awt.*;  
import java.awt.image.BufferedImage;  
import java.io.File;  
import java.io.IOException;  
import java.util.ArrayList;  
import java.util.HashMap;  
import java.util.List;  
import java.util.Map;  
import java.util.Random;  
  
@Component  
public class QRcodeUtils {  
  
    // 二维码颜色==黑色  
    private static final int BLACK = 0xFF000000;  
    // 二维码颜色==白色  
    private static final int WHITE = 0xFFFFFFFF;  
    // 二维码图片格式==jpg和png两种  
    private static final List<String> IMAGE_TYPE = new ArrayList<>();  
  
    static {  
        IMAGE_TYPE.add("jpg");  
        IMAGE_TYPE.add("png");  
    }  
  
    private static String defaultPath;  
  
    @Value("${platform.filePath}")  
    public void setDefaultPath(String defaultPath) {  
        QRcodeUtils.defaultPath = defaultPath;  
    }  
  
    private static final String tempDir = "qrcode";  
  
    public static String create(String content) {  
        try {  
            String fileId = IkgStringUtils.getUUID();  
            //图片类型  
            String imageType = "jpg";  
            //获取二维码流的形式，写入到目录文件中  
            BufferedImage image = getBufferedImage(content, null, null);  
            //获得随机数  
            Random random = new Random();  
            //生成二维码存放文件  
            String filePath = defaultPath + File.separator + tempDir + File.separator + fileId + ".jpg";  
            File file = new File(filePath);  
            if (!file.exists()) {  
                file.mkdirs();  
            }  
            ImageIO.write(image, imageType, file);  
            return fileId;  
        } catch (IOException e) {  
            e.printStackTrace();  
            return null;  
        }    }  
  
    /**  
     * @param content  
     * @param path  
     * @param size  
     * @param logoPath  
     * @return  
     */  
    public static String create(String content, String path, Integer size, String logoPath) {  
        try {  
            String fileId = IkgStringUtils.getUUID();  
            //图片类型  
            String imageType = "jpg";  
            //获取二维码流的形式，写入到目录文件中  
            BufferedImage image = getBufferedImage(content, size, logoPath);  
            //获得随机数  
            Random random = new Random();  
            //生成二维码存放文件  
            File file = new File(path + File.separator + fileId + ".jpg");  
            if (!file.exists()) {  
                file.mkdirs();  
            }  
            ImageIO.write(image, imageType, file);  
            return fileId;  
        } catch (IOException e) {  
            e.printStackTrace();  
            return null;  
        }    }  
  
    /**  
     * @param content  
     * @param size  
     * @param logoPath  
     * @return  
     */  
    public static BufferedImage getBufferedImage(String content, Integer size, String logoPath) {  
        if (size == null || size <= 0) {  
            size = 250;  
        }        BufferedImage image = null;  
        try {  
            // 设置编码字符集  
            Map<EncodeHintType, Object> hints = new HashMap<>();  
            //设置编码  
            hints.put(EncodeHintType.CHARACTER_SET, "UTF-8");  
            //设置容错率最高  
            hints.put(EncodeHintType.ERROR_CORRECTION, ErrorCorrectionLevel.H);  
            hints.put(EncodeHintType.MARGIN, 1);  
            // 1、生成二维码  
            MultiFormatWriter multiFormatWriter = new MultiFormatWriter();  
            BitMatrix bitMatrix = multiFormatWriter.encode(content, BarcodeFormat.QR_CODE, size, size, hints);  
            // 2、获取二维码宽高  
            int codeWidth = bitMatrix.getWidth();  
            int codeHeight = bitMatrix.getHeight();  
            // 3、将二维码放入缓冲流  
            image = new BufferedImage(codeWidth, codeHeight, BufferedImage.TYPE_INT_RGB);  
            for (int i = 0; i < codeWidth; i++) {  
                for (int j = 0; j < codeHeight; j++) {  
                    // 4、循环将二维码内容定入图片  
                    image.setRGB(i, j, bitMatrix.get(i, j) ? BLACK : WHITE);  
                }  
            }  
            //判断是否写入logo图片  
            if (logoPath != null && !"".equals(logoPath)) {  
                File logoPic = new File(logoPath);  
                if (logoPic.exists()) {  
                    Graphics2D g = image.createGraphics();  
                    BufferedImage logo = ImageIO.read(logoPic);  
                    int widthLogo = logo.getWidth(null) > image.getWidth() * 2 / 10 ? (image.getWidth() * 2 / 10) : logo.getWidth(null);  
                    int heightLogo = logo.getHeight(null) > image.getHeight() * 2 / 10 ? (image.getHeight() * 2 / 10) : logo.getHeight(null);  
                    int x = (image.getWidth() - widthLogo) / 2;  
                    int y = (image.getHeight() - heightLogo) / 2;  
                    // 开始绘制图片  
                    g.drawImage(logo, x, y, widthLogo, heightLogo, null);  
                    g.drawRoundRect(x, y, widthLogo, heightLogo, 15, 15);  
                    //边框宽度  
                    g.setStroke(new BasicStroke(2));  
                    //边框颜色  
                    g.setColor(Color.WHITE);  
                    g.drawRect(x, y, widthLogo, heightLogo);  
                    g.dispose();  
                    logo.flush();  
                    image.flush();  
                }  
            }  
        } catch (WriterException e) {  
            e.printStackTrace();  
        } catch (IOException e) {  
            e.printStackTrace();  
        }        return image;  
    }  
  
    /**  
     * @param qrPic  
     * @param logoPic  
     * @param path  
     * @return  
     */  
    public static boolean create(File qrPic, File logoPic, String path) {  
        try {  
            String imageType = path.substring(path.lastIndexOf(".") + 1).toLowerCase();  
            if (!IMAGE_TYPE.contains(imageType)) {  
                return false;  
            }  
            if (!qrPic.isFile() && !logoPic.isFile()) {  
                return false;  
            }  
            //读取二维码图片，并构建绘图对象  
            BufferedImage image = ImageIO.read(qrPic);  
            Graphics2D g = image.createGraphics();  
            //读取Logo图片  
            BufferedImage logo = ImageIO.read(logoPic);  
            //设置logo的大小,最多20%0  
            int widthLogo = logo.getWidth(null) > image.getWidth() * 2 / 10 ? (image.getWidth() * 2 / 10) : logo.getWidth(null);  
            int heightLogo = logo.getHeight(null) > image.getHeight() * 2 / 10 ? (image.getHeight() * 2 / 10) : logo.getHeight(null);  
            // 计算图片放置位置，默认在中间  
            int x = (image.getWidth() - widthLogo) / 2;  
            int y = (image.getHeight() - heightLogo) / 2;  
            // 开始绘制图片  
            g.drawImage(logo, x, y, widthLogo, heightLogo, null);  
            g.drawRoundRect(x, y, widthLogo, heightLogo, 15, 15);  
            //边框宽度  
            g.setStroke(new BasicStroke(2));  
            //边框颜色  
            g.setColor(Color.WHITE);  
            g.drawRect(x, y, widthLogo, heightLogo);  
            g.dispose();  
            logo.flush();  
            image.flush();  
            File newFile = new File(path);  
            if (!newFile.exists()) {  
                newFile.mkdirs();  
            }  
            ImageIO.write(image, imageType, newFile);  
            return true;  
        } catch (Exception e) {  
            e.printStackTrace();  
            return false;  
        }    }  
  
    /**  
     * @param path  
     * @return  
     */  
    public static Result analyze(String path) {  
        try {  
            MultiFormatReader formatReader = new MultiFormatReader();  
            File file = new File(path);  
            if (file.exists()) {  
                BufferedImage image = ImageIO.read(file);  
                LuminanceSource source = new BufferedImageLuminanceSource(image);  
                Binarizer binarizer = new HybridBinarizer(source);  
                BinaryBitmap binaryBitmap = new BinaryBitmap(binarizer);  
                Map hints = new HashMap();  
                hints.put(EncodeHintType.CHARACTER_SET, "UTF-8");  
                Result result = formatReader.decode(binaryBitmap, hints);  
                return result;  
            }  
        } catch (IOException e) {  
            e.printStackTrace();  
        } catch (NotFoundException e) {  
            e.printStackTrace();  
        }        return null;  
    }  
  
    public static String getQrcode(String id) {  
        return defaultPath + File.separator + tempDir + File.separator + id + ".jpg";  
    }  
}
```
### 使用
这里是直接存到本地，并在本地设置好路径访问的方式
**创建**
```java

@PostMapping("/create")  
public Object create(@RequestBody Map<String, Object> param, HttpServletRequest request) {  
    String content = IkgStringUtils.toString(param.get("content"));  
    String picId = QRcodeUtils.create(content);  
    return picId;  
}
```
**回显**
```java
@GetMapping("/showPic")  
public void showPic(HttpServletRequest request, HttpServletResponse response) throws IOException {  
    response.setContentType("text/html; charset=UTF-8");  
    response.setContentType("image/*");  
    String picId = request.getParameter("picId");  
    logger.info("picId:{}", picId);  
    String existedFilePath = null;  
    String ext = "";  
    if (picId.equals("")) {//如果查询图片数据为空，返回系统默认图片  
        picId = defaultImageId;  
    } else {  
    // 这个类根据个人数据库实际情况进行生成
        AttachmentModel attachmentModel = attachmentService.queryById(picId);  
        picId = attachmentModel.getAttachId();  
        ext = attachmentModel.getAttachExt();  
    }    if (isSavedWithExtension && !picId.equals("")) {  
        existedFilePath = filePath + File.separator + "tmpFiles" + File.separator + picId + ext;  
    } else {  
        existedFilePath = filePath + File.separator + "tmpFiles" + File.separator + picId + ".png";  
    }  
    OutputStream os = null;  
    FileInputStream fis = null;  
    try {  
        fis = new FileInputStream(existedFilePath);  
        os = response.getOutputStream();  
        int size = fis.available();  
        byte[] buffer = new byte[size];  
        fis.read(buffer);  
        os.write(buffer);  
        os.flush();  
    } catch (FileNotFoundException e) {  
        e.printStackTrace();  
    } catch (IOException e) {  
        e.printStackTrace();  
    } finally {  
        if (os != null) {  
            os.close();  
        }  
        if (fis != null) {  
            fis.close();  
        }  
    }  
}
```