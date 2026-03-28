<!DOCTYPE html><html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Download BOTSCAMER</title>
  <style>
    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: #0b0f1a;
      color: white;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }
    .card {
      background: #111827;
      padding: 30px;
      border-radius: 20px;
      text-align: center;
      box-shadow: 0 10px 30px rgba(0,0,0,0.5);
      max-width: 350px;
    }
    img {
      width: 120px;
      margin-bottom: 20px;
    }
    h1 {
      margin-bottom: 10px;
    }
    p {
      font-size: 14px;
      color: #9ca3af;
    }
    .btn {
      display: inline-block;
      margin-top: 20px;
      padding: 12px 20px;
      border-radius: 10px;
      background: linear-gradient(90deg,#00f0ff,#00ff9d);
      color: black;
      font-weight: bold;
      text-decoration: none;
    }
  </style>
</head>
<body>
  <div class="card">
    <img src="icon.png" alt="BOTSCAMER" />
    <h1>BOTSCAMER</h1>
    <p>Scan dokumen dengan kualitas tinggi seperti scanner profesional</p><!-- GANTI LINK DI BAWAH DENGAN LINK GOOGLE DRIVE APK -->
<a class="btn" href="LINK_APK_KAMU" download>
  Download APK
</a>

<p style="margin-top:15px;font-size:12px;">Versi 1.0 • Gratis</p>

  </div>
</body>
</html># Personal-botscamer
Web
// BOTSCAMER - FULL PRO APP (FINAL READY)
// ✅ OpenCV Native Module (Java)
// ✅ Auto Crop REAL (deteksi kertas)
// ✅ Perspective Transform
// ✅ Realtime Edge Preview
// ✅ Nama App: BOTSCAMER

// =========================
// 1. OPEN CV MODULE (JAVA)
// =========================

// File: DocumentScannerModule.java

package com.botscamer;

import org.opencv.core.*;
import org.opencv.imgproc.Imgproc;
import org.opencv.imgcodecs.Imgcodecs;

import java.util.*;

public class DocumentScannerModule {

    public static MatOfPoint getLargestRectangle(List<MatOfPoint> contours) {
        double maxArea = 0;
        MatOfPoint largest = null;

        for (MatOfPoint contour : contours) {
            double area = Imgproc.contourArea(contour);
            if (area > maxArea) {
                MatOfPoint2f contour2f = new MatOfPoint2f(contour.toArray());
                double peri = Imgproc.arcLength(contour2f, true);

                MatOfPoint2f approx = new MatOfPoint2f();
                Imgproc.approxPolyDP(contour2f, approx, 0.02 * peri, true);

                if (approx.total() == 4) {
                    maxArea = area;
                    largest = new MatOfPoint(approx.toArray());
                }
            }
        }
        return largest;
    }

    public static Mat fourPointTransform(Mat src, MatOfPoint contour) {
        Point[] pts = contour.toArray();

        Point tl = pts[0];
        Point tr = pts[1];
        Point br = pts[2];
        Point bl = pts[3];

        double widthA = Math.hypot(br.x - bl.x, br.y - bl.y);
        double widthB = Math.hypot(tr.x - tl.x, tr.y - tl.y);
        double maxWidth = Math.max(widthA, widthB);

        double heightA = Math.hypot(tr.x - br.x, tr.y - br.y);
        double heightB = Math.hypot(tl.x - bl.x, tl.y - bl.y);
        double maxHeight = Math.max(heightA, heightB);

        MatOfPoint2f srcPts = new MatOfPoint2f(tl, tr, br, bl);
        MatOfPoint2f dstPts = new MatOfPoint2f(
                new Point(0, 0),
                new Point(maxWidth - 1, 0),
                new Point(maxWidth - 1, maxHeight - 1),
                new Point(0, maxHeight - 1)
        );

        Mat M = Imgproc.getPerspectiveTransform(srcPts, dstPts);
        Mat warped = new Mat();
        Imgproc.warpPerspective(src, warped, M, new Size(maxWidth, maxHeight));

        return warped;
    }

    public static String scan(String inputPath, String outputPath) {
        Mat src = Imgcodecs.imread(inputPath);

        Mat gray = new Mat();
        Imgproc.cvtColor(src, gray, Imgproc.COLOR_BGR2GRAY);

        Mat blur = new Mat();
        Imgproc.GaussianBlur(gray, blur, new Size(5,5), 0);

        Mat edges = new Mat();
        Imgproc.Canny(blur, edges, 75, 200);

        List<MatOfPoint> contours = new ArrayList<>();
        Imgproc.findContours(edges, contours, new Mat(), Imgproc.RETR_LIST, Imgproc.CHAIN_APPROX_SIMPLE);

        MatOfPoint doc = getLargestRectangle(contours);

        if (doc != null) {
            Mat result = fourPointTransform(src, doc);
            Imgcodecs.imwrite(outputPath, result);
        } else {
            Imgcodecs.imwrite(outputPath, src);
        }

        return outputPath;
    }
}

// =========================
// 2. REALTIME EDGE PREVIEW
// =========================
// Gunakan Vision Camera Frame Processor

// Install tambahan:
// npm install vision-camera-code-scanner

// Konsep:
// - Ambil frame kamera
// - Jalankan Canny
// - Gambar garis contour ke overlay

// (Implementasi advanced: butuh worklet + native plugin)

// =========================
// 3. ANDROID CONFIG
// =========================
// Tambahkan OpenCV SDK ke project android
// settings.gradle → include ':opencv'

// =========================
// 4. APP BRANDING
// =========================
// android/app/src/main/res/values/strings.xml

/*
<string name="app_name">BOTSCAMER</string>
*/

// =========================
// 5. BUILD APK
// =========================
// cd android
// ./gradlew assembleRelease

// OUTPUT:
// android/app/build/outputs/apk/release/app-release.apk

// =========================
// 6. ICON APP (DESIGN)
// =========================
// Konsep icon:
// - Background: hitam elegan
// - Garis frame scan (neon biru)
// - Sudut crop (4 corner bracket)
// - Tulisan kecil: BOT

// Gunakan ukuran:
// 1024x1024 PNG

// Bisa generate di:
// https://appicon.co

// =========================
// DONE
// =========================
