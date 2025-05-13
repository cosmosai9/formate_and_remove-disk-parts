# formate_and_remove-disk-parts

سكربت Batch مع واجهة نصية في Windows:

```js
@echo off
cls
echo ========================================
echo  سكربت لحذف جميع الأقسام على قرص خارجي
echo ========================================
echo.
echo اختر رقم القرص الذي تريد العمل عليه:
echo.

:: عرض الأقراص المتصلة
diskpart /s list_disks.txt

:: طلب من المستخدم إدخال رقم القرص
set /p disk_num=ادخل رقم القرص: 

:: تأكيد الاختيار من المستخدم
echo أنت اخترت القرص رقم %disk_num%. هل أنت متأكد؟ (y/n)
set /p confirm=اختيارك: 
if /i "%confirm%" NEQ "y" goto end

:: تنفيذ أوامر Diskpart لإزالة جميع الأقسام على القرص المحدد
echo clean | diskpart /s clean_partition.txt

:: إنشاء قسم جديد
echo create partition primary | diskpart /s create_partition.txt

:: تنسيق القسم باستخدام NTFS
echo format fs=ntfs quick | diskpart /s format_partition.txt

:: تعيين حرف للمحرك
echo assign letter=E | diskpart /s assign_drive_letter.txt

echo عملية التنسيق تمت بنجاح!
pause
goto end

:end
echo تم إنهاء السكربت.
pause
```


مثال لواجهة المستخدم (GUI) باستخدام PyQt5 و Python:


```js
import sys
import os
from PyQt5.QtWidgets import QApplication, QWidget, QPushButton, QVBoxLayout, QComboBox, QLabel, QMessageBox
from PyQt5.QtCore import Qt

class DiskFormatter(QWidget):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("Disk Formatter")
        self.setGeometry(100, 100, 400, 300)

        # إنشاء واجهة المستخدم
        self.init_ui()

    def init_ui(self):
        # إضافة الأزرار والقوائم
        self.layout = QVBoxLayout()

        self.info_label = QLabel("اختر القرص الذي تريد تنسيقه:", self)
        self.layout.addWidget(self.info_label)

        # قائمة لاختيار القرص
        self.disk_combo = QComboBox(self)
        self.populate_disks()
        self.layout.addWidget(self.disk_combo)

        self.format_button = QPushButton('تنسيق القرص', self)
        self.layout.addWidget(self.format_button)
        self.format_button.clicked.connect(self.format_disk)

        self.clear_button = QPushButton('مسح جميع الأقسام', self)
        self.layout.addWidget(self.clear_button)
        self.clear_button.clicked.connect(self.clear_partitions)

        # تعيين التخطيط
        self.setLayout(self.layout)

    def populate_disks(self):
        # تحميل الأقراص المتاحة في النظام
        disk_list = self.get_disks()
        self.disk_combo.addItems(disk_list)

    def get_disks(self):
        # جلب الأقراص من النظام باستخدام diskpart
        command = 'diskpart /s list_disks.txt'
        result = os.popen(command).read()
        disks = []

        # استخراج الأقراص من النتيجة
        for line in result.splitlines():
            if "Disk" in line:
                disks.append(line.strip())

        return disks

    def format_disk(self):
        disk = self.disk_combo.currentText()
        if disk:
            disk_number = disk.split()[1]
            confirmation = QMessageBox.question(self, "تأكيد", f"هل أنت متأكد من أنك تريد تنسيق {disk}؟", 
                                               QMessageBox.Yes | QMessageBox.No, QMessageBox.No)

            if confirmation == QMessageBox.Yes:
                # تنفيذ عملية التنسيق
                command = f'echo clean | diskpart /s clean_partition.txt'
                os.system(command)
                command = f'echo create partition primary | diskpart /s create_partition.txt'
                os.system(command)
                command = f'echo format fs=ntfs quick | diskpart /s format_partition.txt'
                os.system(command)
                command = f'echo assign letter=E | diskpart /s assign_drive_letter.txt'
                os.system(command)

                QMessageBox.information(self, "نجاح", f"تم تنسيق {disk} بنجاح!")
        else:
            QMessageBox.warning(self, "تحذير", "يرجى اختيار قرص للتنسيق!")

    def clear_partitions(self):
        disk = self.disk_combo.currentText()
        if disk:
            disk_number = disk.split()[1]
            confirmation = QMessageBox.question(self, "تأكيد", f"هل أنت متأكد من أنك تريد مسح جميع الأقسام على {disk}؟", 
                                               QMessageBox.Yes | QMessageBox.No, QMessageBox.No)

            if confirmation == QMessageBox.Yes:
                # مسح جميع الأقسام
                command = f'echo clean | diskpart /s clean_partition.txt'
                os.system(command)

                QMessageBox.information(self, "نجاح", f"تم مسح جميع الأقسام على {disk} بنجاح!")
        else:
            QMessageBox.warning(self, "تحذير", "يرجى اختيار قرص للمسح!")

if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = DiskFormatter()
    window.show()
    sys.exit(app.exec_())

```
