# domashka3

import os
from PyQt5.QtWidgets import (
    QApplication, QWidget,
    QFileDialog,
    QLabel, QPushButton, QListWidget,
    QHBoxLayout, QVBoxLayout
)
from PIL import Image, ImageFilter
from PyQt5.QtCore import Qt # зміна розміру зображень Qt.KeepAspectRatio
from PyQt5.QtGui import QPixmap # для показу на екрані картинки
app = QApplication([])
win = QWidget()
win.setWindowTitle('PHOTOSHOP')
win.resize(700, 500)

papka = QPushButton('Папка')
photo_list = QListWidget()


photo = QLabel('Тут буде відображатися картинка')


btn_left = QPushButton('Ліворуч')
btn_right = QPushButton('Праворуч')
btn_mirror = QPushButton('Дзеркало')
btn_sharp = QPushButton('Різкість')
btn_bw = QPushButton('Ч/Б')

line = QHBoxLayout()
col1 = QVBoxLayout()
col2 = QVBoxLayout()

col1.addWidget(papka)
col1.addWidget(photo_list)

col2.addWidget(photo, 90)
row = QHBoxLayout()
row.addWidget(btn_left)
row.addWidget(btn_right)
row.addWidget(btn_mirror)
row.addWidget(btn_sharp)
row.addWidget(btn_bw)
col2.addLayout(row)

line.addLayout(col1, 25)
line.addLayout(col2, 75)
win.setLayout(line)

win.show()

fulldir = '' #змінна що буде зберігати шлях до обраної папки
def Fulldir():
    global fulldir
    fulldir = QFileDialog.getExistingDirectory()
    
def filter(files, excentions):
    filter_files = []
    for filename in files:
        for ext in excentions:
            if filename.endswith(ext):
                filter_files.append(filename)
    return filter_files
        
def showFilenames():
    extentions = ['.png', '.jpg', '.jpeg', '.bmp']
    Fulldir()
    filenames = filter(os.listdir(fulldir), extentions)
    photo_list.clear()
    for filename in filenames:
        photo_list.addItem(filename)
        
papka.clicked.connect(showFilenames)

class Image():
    def __init__(self):
        self.image = None
        self.dir = None
        self.image_name = None
        self.save_dir = 'Відредаговані фото'
        
    def loadImage(self, dir, image_name):
        self.dir = dir
        self.image_name = image_name
        file_path = os.path.join(dir, image_name)
        self.image = Image.open(file_path)

    def saveImage(self):
        join_name = os.path.join(self.dir, self.save_dir)
        if not os.path.isdir(join_name):
            os.mkdir(join_name)
        join_file = os.path.join(join_name, self.image_name)
        self.image.save(join_file)



    def showPhoto(self, way):
        photo.hide()
        pixmap = QPixmap(way)
        w = photo.width()
        h = photo.height()
        pixmap = pixmap.scaled(w, h, Qt.KeppAspectRatio)
        photo.setPixmap(pixmap)
        photo.show()

    def do_left(self):
        self.image = self.image.transpose(image.ROTATE_90)
        self.saveImage()
        path_s = os.path.join(fulldir, self.save_dir, self.image_name)
        self.showPhoto(path_s)

    def do_right(self):
        self.image = self.image.transpose(image.ROTATE_270)
        self.saveImage()
        path_s = os.path.join(fulldir, self.save_dir, self.image_name)
        self.showPhoto(path_s)

    def do_mirror(self):
        self.image = self.image.transpose(image.FLIP_LEFT_RIGHT)
        self.saveImage()
        path_s = os.path.join(fulldir, self.save_dir, self.image_name)
        self.showPhoto(path_s)

    def do_sharp(self):
        self.image = self.image.filter(ImageFiler.SHARPEN)
        self.saveImage()
        path_s = os.path.join(fulldir, self.save_dir, self.image_name)
        self.showPhoto(path_s)

    def do_bw(self):
        self.image = self.image.convert('L')
        self.saveImage()
        path_s = os.path.join(fulldir, self.save_dir, self.image_name)
        self.showPhoto(path_s)


obdject = Image()
def showImage(self):
    if photo_list.currentRow()>= 0:
        image_name = photo_list.currentItem().text()
        obdject.loadImage(image_name)
        obdject.showPhoto(os.path.join(obdject.dir, obdject.image_name))
        
        
photo_list.currentRowChanged.connect(showImage)

btn_left.clicked.connect(obdject.do_left)
btn_right.clicked.connect(obdject.do_right)
btn_mirror.clicked.connect(obdject.do_mirror)
btn_sharp.clicked.connect(obdject.do_sharp)
btn_bw.clicked.connect(obdject.do_bw)

app.exec_()
