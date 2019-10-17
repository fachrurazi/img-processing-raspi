Tahap 0 (koneksi VNC)
Aktifkan vnc di preferences
Trus cari ip confignya di ifconfig
Sudo Raspi-config
Tahap 1 (color detection)
Program di raspi
# untuk raspi
Install opencv (pastikan kapasitas tersedia)
•	Gunakan os 2019-07-10 raspbian-buster-full-img (file tahap 1)
•	Sudo apt-get update
•	Sudo apt-get upgrade
•	Setting os, preferences dan tampilkan apk python (enable camera dll)
•	Pastikan install python dan python 3 dan jalankan
•	Sudo apt install python-opencv
•	Sudo apt install python3-opencv
•	Tahap testing
•	Python3
•	Import cv2
•	Import numpy as np
•	Quit()
•	Running camera
•	Import cv2
•	Import numpy as np
•	Cap = cv2.VidoCapture(0)
•	While True:
	_, frame = cap.read()
	Cv2.imshow(“frame”,frame)
	Key = cv2.waitkey(1)
	If key ==27;
•	Break
•	Cap.release()
•	Cv2.destroyAllWindows()
•	Install imutls
•	Sudo pip install imutils
•	Sudo pip3 install imutils
•	Sudo apt install argparde
•	Sudo raspi-config (configuration)
Source : pysource.com/2018/10/31/raspberry-pi-3-and-opencv-3-installation-tutorial/.

_____________________________________________________________________________________



#untuk windows
Sama saja, hanya berbeda perintah penginstallan
•	Pip install opencv-python
•	Pip install numpy
•	Pip install imutils
•	Python -m pip install -U pip
_____________________________________________________________________________________

# Program untuk hsv color space raspi dan win
C:\Users\fachr\Downloads\skripsi> python .\hsv_color.py
import cv2
import numpy as np

def nothing (x):
	pass

cap = cv2.VideoCapture(0)
cv2.namedWindow("Trackbars")

cv2.createTrackbar("L - H", "Trackbars", 0, 255, nothing)
cv2.createTrackbar("L - S", "Trackbars", 0, 255, nothing)
cv2.createTrackbar("L - V", "Trackbars", 0, 255, nothing)
cv2.createTrackbar("U - H", "Trackbars", 255, 255, nothing)
cv2.createTrackbar("U - S", "Trackbars", 255, 255, nothing)
cv2.createTrackbar("U - V", "Trackbars", 255, 255, nothing)




while True:
	_, frame = cap.read()
	hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)

	l_h = cv2.getTrackbarPos ("L - H", "Trackbars")
	l_s = cv2.getTrackbarPos ("L - S", "Trackbars")
	l_v = cv2.getTrackbarPos ("L - V", "Trackbars")
	u_h = cv2.getTrackbarPos ("U - H", "Trackbars")
	u_s = cv2.getTrackbarPos ("U - S", "Trackbars")
	u_v = cv2.getTrackbarPos ("U - V", "Trackbars")

	lower_blue = np.array([l_h, l_s, l_v])
	upper_blue = np.array([u_h, u_s, u_v])
	mask = cv2.inRange(hsv, lower_blue, upper_blue)

	result = cv2.bitwise_and(frame, frame, mask=mask)

	cv2.imshow("frame", frame)
	cv2.imshow("mask", mask)
	cv2.imshow("result", result)

	key = cv2.waitKey(1)
	if key == 27:
		break

cap.release()
cv2.destroyAllWindowsa()

_____________________________________________________________________________________
# Program untuk color detection raspi dan win traffic light
PS C:\Users\fachr\Downloads\skripsi> python .\color_detection.py
from collections import deque
from imutils.video import VideoStream
import numpy as np
import argparse
import cv2
import imutils
import time

ap = argparse.ArgumentParser()
ap.add_argument("-v", "--video",
    help="path to the (optional) video file")
args = vars(ap.parse_args())

greenLower = (48, 0, 199)
greenUpper = (101, 168, 255)
redLower = (0, 0, 176)
redUpper = (28, 72, 255)
yellowLower = (0, 72, 206)
yellowUpper = (75, 255, 255)

if not args.get("video", False):
    vs = VideoStream(src=0).start()
else:
    vs = cv2.VideoCapture(args["video"])

time.sleep(2.0)
while True:
    frame = vs.read()
    frame = frame[1] if args.get("video", False) else frame
    if frame is None:
        break

    frame = imutils.resize(frame, width=600)
    blurred = cv2.GaussianBlur(frame, (11, 11), 0)
    hsv = cv2.cvtColor(blurred, cv2.COLOR_BGR2HSV)

    mask = cv2.inRange(hsv, redLower, redUpper)
    mask = cv2.erode(mask, None, iterations=2)
    mask = cv2.dilate(mask, None, iterations=2)
    
    cnts = cv2.findContours(mask.copy(), cv2.RETR_EXTERNAL,
        cv2.CHAIN_APPROX_SIMPLE)
    cnts = imutils.grab_contours(cnts)
    
    mask1 = cv2.inRange(hsv, yellowLower, yellowUpper)
    mask1 = cv2.erode(mask1, None, iterations=2)
    mask1 = cv2.dilate(mask1, None, iterations=2)
    
    cnts1 = cv2.findContours(mask1.copy(), cv2.RETR_EXTERNAL,
        cv2.CHAIN_APPROX_SIMPLE)
    cnts1 = imutils.grab_contours(cnts1)
    
    mask2 = cv2.inRange(hsv, greenLower, greenUpper)
    mask2 = cv2.erode(mask2, None, iterations=2)
    mask2 = cv2.dilate(mask2, None, iterations=2)
    
    cnts2 = cv2.findContours(mask2.copy(), cv2.RETR_EXTERNAL,
        cv2.CHAIN_APPROX_SIMPLE)
    cnts2 = imutils.grab_contours(cnts2)

    if len(cnts) > 0:
        c = min(cnts, key=cv2.contourArea)
        ((x, y), radius) = cv2.minEnclosingCircle(c)

        if radius > 10:
            cv2.circle(frame, (int(x), int(y)), int(radius),(255, 100, 255), 2)
            cv2.putText(frame, "  red color", (int(x), int(y)), cv2.FONT_HERSHEY_SIMPLEX, 0.7, (0, 255, 255)) 

    if len(cnts1) > 0:
        f = max(cnts1, key=cv2.contourArea)
        ((a, b), radius1) = cv2.minEnclosingCircle(f)

        if radius1 > 10:
            cv2.circle(frame, (int(a), int(b)), int(radius1),(150, 100, 255), 3)
            cv2.putText(frame, "  yellow color", (int(a), int(b)), cv2.FONT_HERSHEY_SIMPLEX, 0.7, (0, 255, 255))
            
    if len(cnts2) > 0:
        g = max(cnts2, key=cv2.contourArea)
        ((d, e), radius2) = cv2.minEnclosingCircle(g)

        if radius2 > 10:
            cv2.circle(frame, (int(d), int(e)), int(radius2),(0, 150, 255), 4)
            cv2.putText(frame, "  green color", (int(d), int(e)), cv2.FONT_HERSHEY_SIMPLEX, 0.7, (0, 255, 255))
    
    cv2.imshow("Frame", frame)
    key = cv2.waitKey(1) & 0xFF

    if key == ord("q"):
        break			//pakai ini lebih enak

if not args.get("video", False):
    vs.stop()

else:
    vs.release()
cv2.destroyAllWindows()
_____________________________________________________________________________________

	Note untuk nilai batas atas dan bawah antar kamera berbeda-beda, jadi harus selalu kallibrasi.

#pengertian atau translate dari program ball tracking yang dijadikan color detection (hsv)
Tujuan yang ingin dicapai sudah cukup jelas yaitu:
Langkah #1: mendeteksi kehadiran bola yang diwarnai menggunakan teknik visi computer.
Langkah #2: mengikuti bola selama berada pada frame video, membuat gambar posisi bola sebelum digerakkan.
Hasil akhir produk harus mirip dengan GIF dan video dibawah ini.
Setelah membaca post pada blog ini, kamu akan mempunyai gambaran yang bagus mengenai bagaimana membuat lintasan/jalur bola di video streams menggunakan python dan openCV.
Ball tracking with OpenCV
Mari kita mulai dengan contoh. Buka file baru, namai dengan ball_tracking.py dan kita akan membuat koding: 
[kode ball tracking with openCV]
Baris 2-8 menangani import paket yang perlu. Kita akan menggunakan deque, list seperti struktur data dengan penambahan super cepat dan pop untuk mempertahankan list dari  N sebelumnya-lokasi dari bola pada video stream. Mempertahankan queue akan membuat kita untuk menggambar ‘contrail’ dari bola yang akan dibuat jalurnya.
Kita juga bisa menggunakan imutils, koleksiku dari OpenCV fungsi kenyamanan untuk membuat beberapa task dasar (seperti resize) lebih mudah. Jika kamu belum mempunyai imutils terinstall pada sistem computer, kamu bisa mendapatkannya di Github atau gunakan pip untuk menginstall. 
1	$ pip install --upgrade imutils

Baris 11-16 mengatur parsing argument command line. Pertukaran pertama –video adalah jalan untuk contoh file video. Jika bisa melakukan switch maka OpenCV akan mengambil pointer ke file video dan membaca frame dari itu. Tetapi jika tidak bisa berhasil melakukan switch maka OpenCV akan mencoba untuk mengakses webcam. 
Jika ini adalah pertama kalinya kamu menjalankan script ini, aku menyarankan untuk menggunakan –video switch untuk memulai: ini akan mendemonstrasikan fungsionalitas script ke kamu lalu kamu bisa memodifikasi script, file video, dan akses webcam sesuai keinginanmu. 
Argument kedua/opsi kedua, --buffer adalah ukuran maksimum dari deque yang mempertahankan list dari koordinat bola sebelum dijalurkan. Deque ini akan memungkinkan kita untuk menggambar ‘contrail’ dari bola, merincikan lokasi sebelumnya. Deque yang pendek akan membuat ekor lebih pendek sedangkan queue yang lebih besar akan membuat ekor yang lebih panjang (karena lebih banyak titik yang akan dijalurkan/dilacak keberadaannya).
Sekarang argument commad line telah terurai, mari kita lihat beberapa kode:
Baris 21 dan 22 mendefinisikan warna garis  tepi/batas yang lebih atas dan bawh pada HSV color space (di mana aku menentukan menggukan skrip pendeteksi range di imutils library). Warna batas ini akan memungkinkan kita untuk mendeteksi bola hijau pada file video. Baris 23 akan menginisiasi deque of pst enggunakan ukuran buffer maksismum yang tersedia (di mana default ke 64).
Mulai dari sini kita harus bisa mendapatkan mengakses ke vs pointer. Jika –video switch tidak mendukung maka kita harus mendapatkan referensi/acuan ke webcam (baris 27 dan 28) dengan cara menggunakan imutils.video  VideoStream untuk efisiensi. Sedangkan jika path file video tersedia, buka lah itu membaca dan mendapatkan pointer pada baris 31 dan 32 (gunakan perintah di cv2.VideoCapture).
Barias 38 mulai menggunakan perulangam yang akan berlanjut sampai (1) kita menekan kunci q yang mengindikasi bahwa kita ingin mengakhiri skrip atau (2) file video yang dibuat telah berakhir dan berjalan keluar dari frame. 
Baris 40 membuat panggilan untuk membaca metode dari titik pada kameran di mana akan mengembalikann 2-tuple. Masukan pertama dari tuple mengambil Boolean yang akan menunjukkan frame sudah sukses terbaca atau tidak. Frame adalah video frame itu sendiri. Barus 43 akan menangani implementasi VideoStream  vs VideoCapture.
Dalam kasus membaca file video dan frame tidak terbaca dengan sukses, itu menunjukkan bahwa kita sudah diakhir video dan bisa berhenti dari perulangan (baris 47 dan 48).
Baris 52-54 melakukan preprocess frame. Pertama kita mengubah size frame sehingga lebarnya menjadi 600px. Memperkecil frame memungkinkan kita untuk memproses frame lebih cepat sehingga bisa menembuat kenaikan FPS (karena gambar yang diproses lebih sedikit). Selanjunnya kita akan mmebuat blur frame untuk mengurangi noise dari frekuensi yang tinggi dan membuat kita fokus kepada objek structural yang ada didalam frame, termasuk bola. Akhirnya kita akan mengubah frame menjadi ruang HSV berwarna. 
Baris 59 merupakan perintah untuk lokalisasi dari bola hijau di frame dengan memanggil cv2.inRange. pertama kita harus menyediakan batas warna HSV yang lebih rendah untuk waran hijau, kemudian diikuti oleh batas HSV yang lebih tinggi. Output dari cv2.inRange adalah mask binary, seperti yang dibawah ini: 
[gambar]
Seperti yang bisa kita lihat, kita sudah bisa mendeteksi bola hijau pada gambar. Erosi dan dilatasi (baris 60 dan 61) menghilangkan semua gumpalan kecil yang mungkin tersisah pada mask. 
Selanjutnya kita akan melakukan perhitungan kontur dari bola hijau dan menggambarnya pada frame.
Kita akan mulai perhitungan kontur objek pada gambar mulai dari baris 65 dan 66.  Pada baris-baris selanjutnya buat fungsi yang cocok dengan semua versi dari OpenCV. Kamu bisa membaca lebih lanjut mengenai kenapa cv2.findContours  ini perubahan ini penting di post ini (yg ada linknya itu). Kita juga harus mengisiasi koordinat pusat  (x, y)- dari bola menjadi pada None  baris ke 68.
Baris 71 membuat pengecekan untuk memastikan kontur terakhir sudah bisa ditemukan pada mask. Asalkan kontur terakhir sudah ada, kita bisa menemukan kontur terbesar di list cnts pad baris ke 75, hitung minimum keliling lingkaran gumpalan dan hitung koordinat pusat (x,y) pada baris ke 77 dan 78.
Pada baris 81, buat pengecekan cepat untuk memastikan radius dari keliling lingkaran sudah cukup besar. Sebut saja jika radius sudah lulus dari ini (maksudnya keliling lingkarannya udh gede) lalu kita akan menggambar dua lingkaran: satu lingkaran ada disekitar bola dan satu lagi mengacu pada centroid bola.
Lalu baris 89 akan menambahkan centroid pada list pts. 
Langkah terakhir adalah menggambar contrail (ngga tau ini apa) pada bola, atau secara simplenya koordinat N (x,y) sudah terdeteksi. Ini juga salah satu proses yang mudah:
Kita mulai melakukan perulangan dari setiap pts pada baris 92. Jika titik saat ini atau sesudahnya None (mengacu bahwa bola tidak sukses terdeteksi pada frame), abaikan index sekarang dan lanjutkan sisa perulangan pts (baris 95 dan 96).
Jika kedua poin tadi sudah valid, kita akan menghitung thickness dari contrail lalu gambarkan pada frame (barus 100 dan 101).
Kelebihan dari ball_tracking.py  adalah skripnya secara simple melakukan beberapa perawatan (?) dasar dengan cara menampilkan frame pada layar, mendeteksi kunci yang ditekan, dan melepaskan titik vs. 
Ball tracking in action
Sekarang skrip sudah selesai. Mari kita coba. Buka terminal dan jalan command di bawah ini. 
1	$ python ball_tracking.py --video ball_tracking_example.mp4

Command ini akan memulai skrip menggunakan ball_tracking_example.mp4  demo video. Di bawah ini kamu akan lihat beberapa animasi GIF yang sudah bisa mendeteksi dan menbuat jalur menggunakan OpenCV. 
[video]
Kemudia jika kamu ingin menjalankan skrip menggunakan webcam daripada file video yang disediakan, hilangkan --video  switch:
1	$ python ball_tracking.py

Jika kamu ingin melihat hasil yang di atas kamu membutuhkan objek berwarna hijau yang memiliki range warna HSV yang sama dengan yang saya gunakan untuk mendemontrasikan video ini. 
Kesimpulan 
Pada postingan blog ini kita belajar bagaimana membuat jalur bola menggunakan OpenCV. Skrip python yang kita kembangkan (maksudnya mungkin dibuat diblog ini) akan bisa (1) mendeteksi keberadaan dari bola yang diwarnai, kemudia (2) menggambarkan jalur atau track bola selama bola itu bergerak didalam screen.
Seperti yang terlihat pada hasil, sistem ini cukup baik dan bisa membuat lintasan bola walaupun terhalang oleh tangan saya. 
Skrip ini juga bisa mengoperasikan frame rate yang sangat tinggi (>32 FPS), yang artinya bisa menunjukkan bahwa metode tracking berdasarkan warna  adalah cocok untuk mendeteksi real-time detection dan tracking (membuat jalur).








Tahap 2 (tensorflow object detection-API)
# tahapan untuk windows
https://medium.com/@986110101/object-detection-afbcad52fd47
•	install python v3.6.3 (web)
•	install tensorflow v1.14 sama cuda juga, trus cudnn juga, nanti zip dimasukin ke prog file bagian nvidia computing toolkit
•	Buat terlebih dahulu folder baru, misal c:\deteksi>
•	Buka command prompt window
•	kemudian arahkan pada folder c:\deteksi> dan ketikkan

pip install tensorflow-gpu==1.14
bila telah selesai instalasi tensorflow-nya, dari command prompt window ketikkan perintah berikut,
C:\deteksi>python
maka dalam command prompt akan masuk dalam shell python,
C:\deteksi>python
Python 3.6.3 (tags/v3.6.8:3c6b436a57, Dec 24 2018, 00:16:47) [MSC v.1916 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>>
untuk cek keberhasilan instalasi ketikkan perintah berikut
>>> import tensorflow as tf
kemudian tunggu sebentar, biarkan berproses, ketikkan,
>>> hello = tf.constant('Hello, TensorFlow!')
>>> sess = tf.Session()
maka akan muncul seperti ini,
>>> hello = tf.constant('Hello, TensorFlow!')
>>> sess = tf.Session()
2019-02-11 09:48:04.904123: I tensorflow/core/platform/cpu_feature_guard.cc:141] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX2
kemudian, ketikkan
>>> print(sess.run(hello))
hasilnya
b'Hello, TensorFlow!'
sampai disini berarti anda telah sukses menginstall TensorFlow!

kemudian masuklah kembali ke command prompt window yang baru untuk menginstall package tersebut satu persatu, dan menuju folder c:\deteksi> kemudian ketikkan
•	pip install pillow
•	pip install lxml
•	pip install jupyter
•	pip install matplotlib
•	pip install opencv-python
Tahap instalasi model object detection
kunjungi website berikut,
https://github.com/tensorflow/models
kemudian klik “clone/download” pilihlah download ZIP,
maka akan didapatkan hasil download berupa sebuah folder extrak bernama “models-master”.
kemudian arahkan folder models-master tersebut dibawah direktori c:/deteksi> dan kemudian diekstraksi.
maka didapatkan folder berikut,
deteksi/models-master/
  |- official
  |- research
  |- samples
  |- tutorials
  |- ...
kemudian di rename “models-master” menjadi “models”
deteksi/models/
  |- official
  |- research
  |- samples
  |- tutorials
  |- ...
karena nantinya banyak scripts yang digunakan di dalam folder c:/deteksi/models/research/object_detection> maka sebaiknya ditambahkan path tersebut di dalam environment windows, termasuk folder lainnya.
caranya, pada start windows, kolom search, ketikkan environment, kemudian ketikkan dan pilihlah “system variables”
C:/deteksi/models/research/object_detection
C:/deteksi/models/research/slim
C:/deteksi/models/research/slim/datasets
C:/deteksi/models/research/slim/deployment
C:/deteksi/models/research/slim/nets
C:/deteksi/models/research/slim/preprocessing
C:/deteksi/models/research/slim/scripts

Tahap instalasi Protobuf
Download protobuf dari object detection api ini yang versi 3.4 win32 di
https://github.com/google/protobuf/releases
buatlah folder baru (dengan nama terserah, misal “deteksiproto”) untuk menampung file zip protobuf tersebut, kemudian di ekstrak, kemudian pindahkan file protobuf yang berada di folder bin ke dalam c:/deteksi/models/research>
kemudian buka kembali command prompt yang baru, dan arahkan ke c:/deteksi/models/research/ kemudian ketikkan,
protoc object_detection/protos/*.proto --python_out=.
pada perintah diatas ada “dot” jangan sampai tertinggal
kemudian cek apakah tensorflow api sudah terpasang dengan baik dengan mengetikkan dari command prompt yang telah diarahkan ke c:/deteksi/models/research/
python object_detection/builders/model_builder_test.py
jika muncul kesalahan seperti berikut,
File “object_detection/builders/model_builder_test.py”, line 23, in <module>
 from object_detection.builders import model_builder
ModuleNotFoundError: No module named ‘object_detection’
maka carilah file model_builder_test.py yang berada di folder c:/deteksi/models/research/object_detection/builders> dan kemudian tambahkan perintah berikut setelah perintah import tensorflow as tf
import tensorflow as tfimport sys
sys.path.append("C:\\deteksi\\models\\research\\")
sys.path.append("C:\\deteksi\\models\\research\\object_detection\\utils")
sys.path.append("C:\\deteksi\\models\\research\\slim")
sys.path.append("C:\\deteksi\\models\\research\\slim\\nets")
jika telah berhasil tanpa kesalahan maka siap masuk pada persiapan berikutnya,

Tahap instalasi labelImg
Untuk instalasi labelimg dapat download dulu di
https://github.com/tzutalin/labelImg
kemudian simpan folder (labelImg-master.zip) tersebut di c:/deteksi> dan kemudian di ekstrak, setelah itu nama labelImg-master diganti menjadi labelImg saja.
setelah itu, buka kembali command prompt baru, dan arahkan ke c:/deteksi/labelImg> kemudian ketikkan berikut
pip install PyQt5
pip install lxml
pyrcc5 -o resources.py resources.qrc
kemudian ketikkan
python labelImg.py

klo ada error, pindahin resourcesnya (copi) ke libs

maka terbukalah jendela baru berupa aplikasi labelImg, pelabelan image untuk dibaca oleh sistem.
sebelumnya buatlah empat folder dibawah c:/deteksi>
yakni folder annotations, data, images dan training.
Semua gambar yang akan anda gunakan dalam object detection simpanlah di folder images dan buatlah dua folder dibawah folder images, yakni train dan test, sebagian besar (80%) simpanlah di folder train sedangkan sisanya (20%nya) simpanlah di folder test. Didalam folder annotations juga buatlah dua folder train dan test.

bukalah folder dimana gambar dalam folder train disimpan, jika gambar telah muncul maka klik “create rect box” disamping kiri, kemudian arahkan kursor pada object yang diincar, drag secara kotak ke object yang dimaksud, kemudian lepaskan, maka akan muncul jendela kecil pelabelan, silahkan diketik nama object tersebut, kemudian jangan lupa di save, dan arahkan folder save nya ada di c:/deteksi/annotations/train> untuk gambar berikutnya bagi object yang diincar dengan cara yang sama, untuk label yang sama maka tidak perlu menuliskan ulang, akan muncul dengan sendirinya, namun case-sensitive.
setelah semua gambar diberikan label seperti yang diinginkan maka menginjak pada tahap berikutnya.

Convert XML ke CSV
karena proses pe-label-an menggunakan labelImg menghasilkan semua file gambar XML, maka kita rubah file XML ini menjadi CSV dengan perintah seperti berikut,
import os
import glob
import pandas as pd
import xml.etree.ElementTree as ETdef xml_to_csv(path):
    xml_list = []
    for xml_file in glob.glob(path + '/*.xml'):
        tree = ET.parse(xml_file)
        root = tree.getroot()
        for member in root.findall('object'):
            value = (root.find('filename').text,
                     int(root.find('size')[0].text),
                     int(root.find('size')[1].text),
                     member[0].text,
                     int(member[4][0].text),
                     int(member[4][1].text),
                     int(member[4][2].text),
                     int(member[4][3].text)
                     )
            xml_list.append(value)
    column_name = ['filename', 'width', 'height', 'class', 'xmin', 'ymin', 'xmax', 'ymax']
    xml_df = pd.DataFrame(xml_list, columns=column_name)
    return xml_dfdef main():
    for directory in ['train', 'test']:
        image_path = os.path.join(os.getcwd(), 'annotations/{}'.format(directory))
        xml_df = xml_to_csv(image_path)
        xml_df.to_csv('data/{}_labels.csv'.format(directory), index=None)
        print('Successfully converted xml to csv.')main()
kemudian simpanlah kode program diatas dengan nama xml_to_csv.py di direktori c:/deteksi> lalu buka kembali command prompt dan arahkan ke c:/deteksi> kemudian ketikkan perintah berikut,
python xml_to_csv.py
jika ketika menjalankan perintah tersebut terdapat error, “no module name pandas”, maka jalankan dulu perintah ini,
pip install pandas
kemudian ulangi lagi perintah ini,
python xml_to_csv.py
jika tidak ada kendala, maka seluruh gambar dengan ekstensi file xml telah dirubah kedalam bentuk csv.

Convert CSV ke dalam TFRECORD
Agar data dapat diproses dalam Tensorflow maka data csv akan dirubah ke bentuk TFRECORD, perintah untuk merubah tersebut ada pada koding berikut,
from __future__ import division
from __future__ import print_function
from __future__ import absolute_import
import os
import io
import pandas as pd
import tensorflow as tf
import sys
sys.path.append("C:\\deteksi\\models\\research\\")
sys.path.append("C:\\deteksi\\models\\research\\object_detection\\utils")
sys.path.append("C:\\deteksi\\models\\research\\slim")
sys.path.append("C:\\deteksi\\models\\research\\slim\\nets")from PIL import Image
from object_detection.utils import dataset_util
from collections import namedtuple, OrderedDictflags = tf.app.flags
flags.DEFINE_string('type', '', 'Type of CSV input (train/test)')
flags.DEFINE_string('csv_input', '', 'Path to the CSV input')
flags.DEFINE_string('output_path', '', 'Path to output TFRecord')
FLAGS = flags.FLAGSdef class_text_to_int(row_label):
    if row_label == 'Leminerale':
        return 1
    if row_label == 'Aqua':
        return 2
    else:
        return 0def split(df, group):
    data = namedtuple('data', ['filename', 'object'])
    gb = df.groupby(group)
    return [data(filename, gb.get_group(x)) for filename, x in zip(gb.groups.keys(), gb.groups)]def create_tf_example(group, path):
    with tf.gfile.GFile(os.path.join(path, '{}'.format(group.filename)), 'rb') as fid:
        encoded_jpg = fid.read()
    encoded_jpg_io = io.BytesIO(encoded_jpg)
    image = Image.open(encoded_jpg_io)
    width, height = image.size    filename = group.filename.encode('utf8')
    image_format = b'jpg'
    xmins = []
    xmaxs = []
    ymins = []
    ymaxs = []
    classes_text = []
    classes = []    for index, row in group.object.iterrows():
        xmins.append(row['xmin'] / width)
        xmaxs.append(row['xmax'] / width)
        ymins.append(row['ymin'] / height)
        ymaxs.append(row['ymax'] / height)
        classes_text.append(row['class'].encode('utf8'))
        classes.append(class_text_to_int(row['class']))    tf_example = tf.train.Example(features=tf.train.Features(feature={
       'image/height': dataset_util.int64_feature(height),
       'image/width': dataset_util.int64_feature(width),
       'image/filename': dataset_util.bytes_feature(filename),
       'image/source_id': dataset_util.bytes_feature(filename),
       'image/encoded': dataset_util.bytes_feature(encoded_jpg),
       'image/format': dataset_util.bytes_feature(image_format),
       'image/object/bbox/xmin': dataset_util.float_list_feature(xmins),
       'image/object/bbox/xmax': dataset_util.float_list_feature(xmaxs),
       'image/object/bbox/ymin': dataset_util.float_list_feature(ymins),
       'image/object/bbox/ymax': dataset_util.float_list_feature(ymaxs),
       'image/object/class/text': dataset_util.bytes_list_feature(classes_text),
       'image/object/class/label': dataset_util.int64_list_feature(classes),
    }))
    return tf_exampledef main(_):
    writer = tf.python_io.TFRecordWriter(FLAGS.output_path)
    path = os.path.join(os.getcwd(), 'images/{}'.format(FLAGS.type))
    examples = pd.read_csv(FLAGS.csv_input)
    grouped = split(examples, 'filename')
    for group in grouped:
        tf_example = create_tf_example(group, path)
        writer.write(tf_example.SerializeToString())    writer.close()
    output_path = os.path.join(os.getcwd(), FLAGS.output_path)
    print('Successfully created the TFRecords: {}'.format(output_path))if __name__ == '__main__':
    tf.app.run()
simpanlah dengan nama generate_tfrecord.py dalam folder c:/deteksi> kemudian bukalah command prompt dan arahkan pada c:/deteksi> kemudian jalankan perintah berikut
python generate_tfrecord.py --type=train --csv_input=data/train_labels.csv  --output_path=data/train.record
sampai menunjukkan,
“Successfully created the TFRecords: C:\deteksi\data/train.record”
dan
python generate_tfrecord.py --type=test --csv_input=data/test_labels.csv  --output_path=data/test.record
Sampai
“Successfully created the TFRecords: C:\deteksi\data/test.record”
kemudian buatlah file baru melalui notepad++ untuk membuat file label map,
item {
       id: 1
       name: 'Leminerale'
}
item {
       id: 2
       name: 'Aqua'
}
simpan konfigurasi label map tersebut dan diberi nama botol_number_map.pbtxt dan simpanlah dalam folder c:/deteksi/data>

Tahap Konfigurasi Sistem
Copy-lah koding untuk konfigurasi berikut
model {
  ssd {
    num_classes: 2
    box_coder {
      faster_rcnn_box_coder {
        y_scale: 10.0
        x_scale: 10.0
        height_scale: 5.0
        width_scale: 5.0
      }
    }
    matcher {
      argmax_matcher {
        matched_threshold: 0.5
        unmatched_threshold: 0.5
        ignore_thresholds: false
        negatives_lower_than_unmatched: true
        force_match_for_each_row: true
      }
    }
    similarity_calculator {
      iou_similarity {
      }
    }
    anchor_generator {
      ssd_anchor_generator {
        num_layers: 6
        min_scale: 0.2
        max_scale: 0.95
        aspect_ratios: 1.0
        aspect_ratios: 2.0
        aspect_ratios: 0.5
        aspect_ratios: 3.0
        aspect_ratios: 0.3333
      }
    }
    image_resizer {
      fixed_shape_resizer {
        height: 300
        width: 300
      }
    }
    box_predictor {
      convolutional_box_predictor {
        min_depth: 0
        max_depth: 0
        num_layers_before_predictor: 0
        use_dropout: false
        dropout_keep_probability: 0.8
        kernel_size: 1
        box_code_size: 4
        apply_sigmoid_to_scores: false
        conv_hyperparams {
          activation: RELU_6,
          regularizer {
            l2_regularizer {
              weight: 0.00004
            }
          }
          initializer {
            truncated_normal_initializer {
              stddev: 0.03
              mean: 0.0
            }
          }
          batch_norm {
            train: true,
            scale: true,
            center: true,
            decay: 0.9997,
            epsilon: 0.001,
          }
        }
      }
    }
    feature_extractor {
      type: 'ssd_mobilenet_v1'
      min_depth: 16
      depth_multiplier: 1.0
      conv_hyperparams {
        activation: RELU_6,
        regularizer {
          l2_regularizer {
            weight: 0.00004
          }
        }
        initializer {
          truncated_normal_initializer {
            stddev: 0.03
            mean: 0.0
          }
        }
     
   batch_norm {
          train: true,
          scale: true,
          center: true,
          decay: 0.9997,
          epsilon: 0.001,
        }
      }
    }
    loss {
      classification_loss {
        weighted_sigmoid {
          anchorwise_output: true
        }
      }
      localization_loss {
        weighted_smooth_l1 {
          anchorwise_output: true
        }
      }
      hard_example_miner {
        num_hard_examples: 3000
        iou_threshold: 0.99
        loss_type: CLASSIFICATION
        max_negatives_per_positive: 3
        min_negatives_per_image: 0
      }
      classification_weight: 1.0
      localization_weight: 1.0
    }
    normalize_loss_by_num_matches: true
    post_processing {
      batch_non_max_suppression {
        score_threshold: 1e-8
        iou_threshold: 0.6
        max_detections_per_class: 100
        max_total_detections: 100
      }
      score_converter: SIGMOID
    }
  }
}train_config: {
  batch_size: 2
  optimizer {
    rms_prop_optimizer: {
      learning_rate: {
        exponential_decay_learning_rate {
          initial_learning_rate: 0.004
          decay_steps: 800720
          decay_factor: 0.95
        }
      }
      momentum_optimizer_value: 0.9
      decay: 0.9
      epsilon: 1.0
    }
  }
  fine_tune_checkpoint: "ssd_mobilenet_v1_coco_2017_11_17/model.ckpt"
  from_detection_checkpoint: true
  num_steps: 100000
  data_augmentation_options {
    random_horizontal_flip {
    }
  }
  data_augmentation_options {
    ssd_random_crop {
    }
  }
}train_input_reader: {
  tf_record_input_reader {
    input_path: "data/train.record"
  }
  label_map_path: "data/botol_number_map.pbtxt"
}eval_config: {
  num_examples: 30
  max_evals: 10
}eval_input_reader: {
  tf_record_input_reader {
    input_path: "data/test.record"
  }
  label_map_path: "data/botol_number_map.pbtxt"
  shuffle: false
  num_readers: 1
  num_epochs: 1
}
simpanlah dengan nama botol_v1.config dalam c:/deteksi/training>
Susunlah folder seperti berikut,
deteksi
|
|--annotations
|   |--test
|       |--pic20190211_132705.xml
|       |--pic20190211_132715.xml
|       |-- ...
|       |-- ...
| 
|   |--train
|       |--pic20190211_132731.xml
|       |--pic20190211_132742.xml
|       |-- ...
|       |-- ...
|
|--data
|   |--botol_number_map.pbtxt
|   |--test.record
|   |--test_labels.csv
|   |--train.record
|   |--train_labels.csv
|
|--images
|   |--test
|       |--pic20190211_132705.jpeg
|       |--pic20190211_132715.jpeg
|       |-- ...
|       |-- ...
| 
|   |--train
|       |--pic20190211_132731.jpeg
|       |--pic20190211_132742.jpeg
|       |-- ...
|       |-- ...
|
|--training
|   |--botol_v1.config
|
|--generate_tfrecord.py
|
|--xml_to_csv.py
|
|--models
|
|--labelImg

Tahap training model
Mari kita persiapkan tensorflow model yang telah kita susun diatas, ikut langkah berikut,
1.	salinlah folder X, Y dan Z diatas dan copy ke dalam folder c:/deteksi/models/research/object_detection. (folder X, Y, Z silahkan lihat melalui video saya) :)
2.	kemudian downloadlah pre-trained model dari tensorflow berikut,
http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v1_coco_2017_11_17.tar.gz
ada banyak pilihan model pre-trained, saat ini kita gunakan terlebih dahulu ssd_mobilenet_v1_coco_2017_11_17.tar.gz nanti dapat kita coba dengan yang lainnya. Jika sudah berhasil di download, maka esktraklah ssd_mobilenet_v1_coco_2017_11_17.tar.gz tersebut ke dalam folder c:/deteksi/models/research/object_detection>
setelah itu, masuklah ke folder c:/deteksi/models/research/object_detection/legacy> dan copylah file train.py ke folder c:/deteksi/models/research/object_detection>
kemudian jalankan perintah ini melalui command prompt c:/deteksi/models/research/object_detection>
python train.py --logtostderr --train_dir=training --pipeline_config_path=training/botol_v1.config
jika ada kesalahan seperti berikut,
ModuleNotFoundError: No module named 'object_detection'
maka bukalah file train.py melalui editor (seperti notepad++) kemudian tambahkan baris seperti berikut,
import tensorflow as tfimport sys
sys.path.append("C:\\deteksi\\models\\research\\")
sys.path.append("C:\\deteksi\\models\\research\\object_detection\\utils")
sys.path.append("C:\\deteksi\\models\\research\\slim")
sys.path.append("C:\\deteksi\\models\\research\\slim\\nets")
jika tidak ada kesalahan maka, proses training akan mulai berjalan, dan ini membutuhkan waktu yang cukup lama (sekitar 10 jam) tergantung pada kemampuan perangkat keras yang disematkan dalam laptop anda, dan proses berjalan nampak seperti berikut,

 
Untuk melihat proses yang sedang berjalan, dapat menggunakan tensorboard, dengan cara buka command prompt baru dan arahkan ke c:/deteksi/models/research/object_detection> dan ketikkan,
tensorboard --logdir=training 

 
untuk mengeluarkan visualisasi dari proses diatas, bukalah web browser kemudian ketikkan localhost:6006
anda dapat menghentikan proses training jika dirasa loss sudah tidak menunjukkan perubahan lagi, kemudian setelah kembali masuk ke c:/deteksi/models/research/object_detection> kemudian lihatlah dalam folder training, maka akan ditemukan file checkpoint sebagai jejak proses training, terdapat tiga file utama yakni,
model.ckpt-XXXX.data-00000-of-00001
model.ckpt-XXXX.index
model.ckpt-XXXX.meta
maka carilah nilai XXXX yang tertinggi, kemudian ketikkan perintah berikut, (misal disini dipakai nilai tertinggi XXXX = 1345)
python export_inference_graph.py --input_type image_tensor --pipeline_config_path training/botol_v1.config --trained_checkpoint_prefix training/model.ckpt-1345 --output_directory botol_baru
maka akan muncul folder botol_baru di c:/deteksi/models/research/object_detection>
setelah itu copy-lah kode dibawah ini
# coding: utf-8 
# In[1]:  
import numpy as np
import os
import six.moves.urllib as urllib
import sys
import tarfile
import tensorflow as tf
import zipfile  # In[2]:  
from collections import defaultdict
from io import StringIO
from matplotlib import pyplot as plt
from PIL import Image  # In[16]:  
import cv2
cap = cv2.VideoCapture(0)  # In[5]:  # This is needed since the notebook is stored in the object_detection folder.
sys.path.append("..")  # In[7]:  
from utils import label_map_util 
from utils import visualization_utils as vis_util  # In[8]:  
# What model to download.
MODEL_NAME = 'botol_baru'
#MODEL_NAME = 'ssd_mobilenet_v1_coco_11_06_2017'
MODEL_FILE = MODEL_NAME + '.tar.gz'
DOWNLOAD_BASE = 'http://download.tensorflow.org/models/object_detection/' # Path to frozen detection graph. This is the actual model that is used for the object detection.
PATH_TO_CKPT = MODEL_NAME + '/frozen_inference_graph.pb' # List of the strings that is used to add correct label for each box.PATH_TO_LABELS = os.path.join('data', 'botol_number_map.pbtxt') NUM_CLASSES = 2 # In[7]:  # In[9]:  
detection_graph = tf.Graph()
with detection_graph.as_default():  
  od_graph_def = tf.GraphDef()  
  with tf.gfile.GFile(PATH_TO_CKPT, 'rb') as fid:    
    serialized_graph = fid.read()    
    od_graph_def.ParseFromString(serialized_graph)    
    tf.import_graph_def(od_graph_def, name='')  # In[10]:  
label_map = label_map_util.load_labelmap(PATH_TO_LABELS)
categories = label_map_util.convert_label_map_to_categories(label_map, max_num_classes=NUM_CLASSES, use_display_name=True)
category_index = label_map_util.create_category_index(categories)  # In[11]:  def load_image_into_numpy_array(image):  
  (im_width, im_height) = image.size  
  return np.array(image.getdata()).reshape(
      (im_height, im_width, 3)).astype(np.uint8)  # In[12]:  # If you want to test the code with your images, just add path to the images to the TEST_IMAGE_PATHS.
PATH_TO_TEST_IMAGES_DIR = 'test_images'
TEST_IMAGE_PATHS = [ os.path.join(PATH_TO_TEST_IMAGES_DIR, 'image{}.jpg'.format(i)) for i in range(1, 3) ] 
# Size, in inches, of the output images.
IMAGE_SIZE = (12, 8)  # In[ ]:  with detection_graph.as_default():  
  with tf.Session(graph=detection_graph) as sess:    
    while True:      
      ret, image_np = cap.read()      
      # Expand dimensions since the model expects images to have shape: [1, None, None, 3]      
      image_np_expanded = np.expand_dims(image_np, axis=0)
      image_tensor = detection_graph.get_tensor_by_name('image_tensor:0')      
      # Each box represents a part of the image where a particular object was detected.      
      boxes = detection_graph.get_tensor_by_name('detection_boxes:0')      
      # Each score represent how level of confidence for each of the objects.      
      # Score is shown on the result image, together with the class label.      
      scores = detection_graph.get_tensor_by_name('detection_scores:0')      
      classes = detection_graph.get_tensor_by_name('detection_classes:0')      
      num_detections = detection_graph.get_tensor_by_name('num_detections:0')      
      # Actual detection.      
      (boxes, scores, classes, num_detections) = sess.run(          
          [boxes, scores, classes, num_detections],
          feed_dict={image_tensor: image_np_expanded})      
      # Visualization of the results of a detection.      
      vis_util.visualize_boxes_and_labels_on_image_array(          
          image_np,          
          np.squeeze(boxes),          
          np.squeeze(classes).astype(np.int32),
          np.squeeze(scores),          
          category_index,          
          use_normalized_coordinates=True,          
          line_thickness=8)       
      cv2.imshow('object detection', cv2.resize(image_np, (1000,800)))      
      if cv2.waitKey(25) & 0xFF == ord('q'):
        cv2.destroyAllWindows()        
        break
dan berilah nama “(silahkan lihat melalui video youtube saya)” kemudian simpanlah di (silahkan lihat melalui video youtube saya) kemudian jalankan melalui command crompt di (silahkan lihat melalui video youtube saya)
(silahkan lihat melalui video youtube saya)
maka jadilah object detection anda!
referensi:
1.	https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/install.html
2.	https://medium.com/@hafizhan.aliady/lihat-apa-yang-ada-di-box-hijau-begini-cara-membuat-object-detection-menggunakan-tensorflow-api-6d4a6d44e1a
3.	https://imamdigmi.github.io/post/tensorflow-custom-object-detection/

