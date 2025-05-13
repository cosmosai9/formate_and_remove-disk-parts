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
