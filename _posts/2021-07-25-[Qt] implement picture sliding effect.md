---
layout: post
title: [Qt] implement picture sliding effect
category: qt5
tags: [qt5]
---
Show results:  
![ ](/md_blog/public/assets/2021-07-25/8733c8cee9ed5ddb87133a831c2e5409.png)  
Code  
.h file
    #ifndef MAINWINDOW_H #define MAINWINDOW_H #include <QMainWindow> #define IMAGE_WIDTH 300 #define IMAGE_HEIGHT 200 #if _MSC_VER >= 1600 #pragma execution_character_set("utf-8") #endif QT_BEGIN_NAMESPACE namespace Ui { class MainWindow; } QT_END_NAMESPACE class MainWindow : public QMainWindow { Q_OBJECT public: MainWindow(QWidget *parent = nullptr); ~MainWindow(); QStringList getImageFilePath(const QString&); private slots: void on_lineEdit_textChanged(const QString &arg1); void cellClickedEvent(int, int); private: Ui::MainWindow *ui; QStringList img_list; }; #endif // MAINWINDOW_H 
.c file
    #include "mainwindow.h" #include "ui_mainwindow.h" #include <QScroller> #include <QDir> #include <QLabel> #include <QPixmap> #include <QDebug> #include <QListWidget> MainWindow::MainWindow(QWidget *parent) : QMainWindow(parent) , ui(new Ui::MainWindow) { ui->setupUi(this); ui->tableWidget->verticalHeader()->hide(); ui->tableWidget->horizontalHeader()->hide(); ui->tableWidget->setRowCount(1); QSize size = QSize(IMAGE_WIDTH, IMAGE_HEIGHT); ui->tableWidget->setRowHeight(0, size.height()); ui->tableWidget->setHorizontalScrollBarPolicy(Qt::ScrollBarAlwaysOff); ui->tableWidget->setVerticalScrollBarPolicy(Qt::ScrollBarAlwaysOff); ui->tableWidget->setHorizontalScrollMode(QListWidget::ScrollPerPixel); QScroller::grabGesture(ui->tableWidget,QScroller::LeftMouseButtonGesture); connect(ui->tableWidget, &QTableWidget::cellClicked, this, &MainWindow::cellClickedEvent); img_list = getImageFilePath("../img"); } MainWindow::~MainWindow() { delete ui; } void MainWindow::on_lineEdit_textChanged(const QString &arg1) { if(arg1.isEmpty()) return; int count = arg1.toInt(); if(count > img_list.count()) count = img_list.count(); ui->tableWidget->clear(); ui->tableWidget->setColumnCount(count); for (int i = 0; i < count; i++) { ui->tableWidget->setColumnWidth(i, IMAGE_WIDTH); QLabel *label = new QLabel; QImage image(img_list.at(i)); label->setPixmap(QPixmap::fromImage(image.scaled(IMAGE_WIDTH, IMAGE_HEIGHT, Qt::KeepAspectRatioByExpanding))); ui->tableWidget->setCellWidget(0,i,label); } } void MainWindow::cellClickedEvent(int row, int col) { Q_UNUSED(row) qDebug() << col; } QStringList MainWindow::getImageFilePath(const QString &dirpath) { QDir dir(dirpath); QStringList files_path; files_path.clear(); if(!dir.exists()) return files_path; QStringList nameFilters; nameFilters << "*.png" << "*.jpg"; QStringList files_name = dir.entryList(nameFilters, QDir::Files | QDir::Readable, QDir::Name); for (int i = 0; i < files_name.size(); i++) { files_path << dirpath + "/" + files_name.at(i); } return files_path; } 
UI layout  
![ ](/md_blog/public/assets/2021-07-25/82ac528f7f65b68b2acc306a993f6869.png)
