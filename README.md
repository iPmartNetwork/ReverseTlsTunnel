
[آموزش های ویدویی](https://github.com/radkesvat/ReverseTlsTunnel/blob/master/Tutorials.md)

[راهنمای  اجرای مالتی پورت](https://github.com/radkesvat/ReverseTlsTunnel/blob/master/MultiPort.md)

[راهنمای اجرا به صورت سرویس](https://github.com/radkesvat/ReverseTlsTunnel/blob/master/Service.md)

[راهنمای لود بالانسر](https://github.com/radkesvat/ReverseTlsTunnel/blob/master/Loadbalancer.md)

 حتما با اجرای دوباره دستور نصب همیشه اپدیت کنید 

# مقدمه
تونل معکوس که فکر میکنم باهاش آشنا باشید ؛ فرقش با تونل عادی اینه که کانکشن را سرور خارج آغاز میکنه مثل یک بازدید کننده خارجی از یه سایت ایرانی که سرور شما باشه
و بعد از اتصال ؛ دیتا مثل تونل عادی رد و بدل میشه ؛ این روش از تونل های عادی که سرور ایران به خارج کانکشن رو شروع میکنه از نظر دیتکت شدن؛ میشه گفت بهتره چون عموما یه سرور کانکشن به جایی شروع نمی کنه (برای مدت طولانی و تعداد کانکشن همزمان بالا!)


البته هنوز سیستم فیلترینگی که در ایران هست به مرحله این گونه تشخیص ها نرسیده چون همونطور که میدونیم هنوز هم حتی iptable کار میکنه (بستگی به دیتا سنتر و ایپی خارج و هدر http و غیره...)

روش تونل معکوس مدت ها هست که استفاده میشه اولین بار من از طریق ssh اینو دیدم و بعد یک پروژه هم تو گیت هاب دیدم که این رو با node js پیاده کرده بود.

و خوب سعی کردم این پروژه صرفا یه کپی نباشه و مزیت هایی داشته باشه ؛ نمی خوام خیلی تخصصی توضیح بدم ولی به طور کلی فرقی که هست اینه که سرور خارج با سرور ایران با استفاده از دامنه یه سایت ایرانی هند شیک میکنه و بعد دیتا رد و بدل میشه درصورتی که تونل های معکوس دیگه هند شیک بدون دامنه صرفا با public key داشتن (تا جایی که میدونم) 

فرق دومی هم که داره اینه که این پروژه از قابلیت mux استفاده میکنه تا تعداد کانکشن های بین سرور ایران و خارج همیشه حاصل جمع کلاینت ها نباشه و خوب این نکته بولدی هست که باید انجام بشه (در نسخه ۲ این قابلیت به صورت بتا فعال هست ولی پیشناهاد میشه فعلا استفاده نکنید تا اختلال روش بیشتر برسی بشه)

و همچنین این پیاده سازی از تونل معکوس نسبت به روش های دیگه تونل معکوس منابع cpu کمتری مصرف می کند و قابلیت لودبالانسینگ نیز دارد

و فرق های جزئی دیگه که علاقه داشتین کد رو بخونین و به دلخواه تغییر بدین ؛ کد خوانا تر از faketlstunnel نوشته شده برای دوستانی که میخواستن تونل مخصوص خودشونو بنویسن

با روش تونل معکوس ؛ احتمال زیاد میشه از آیپی های کثیف هم استفاده کرد ؛ تست کنید

# نحوه استفاده 

وارد پوشه ی مورد نظرتون برای نصب برنامه بشید مثلا /root
و کلا برنامه یه فایل هست نصب خاصی نداره فقط فایل دانلود میشه توی همون مسیر که هستین و بهتره بوزر root باشین 

با این دستور برنامه رو دانلود کنید هم سرور ایران و هم خارج
```sh
wget  "https://raw.githubusercontent.com/radkesvat/ReverseTlsTunnel/master/install.sh" -O install.sh && chmod +x install.sh && bash install.sh 
```

و مراحل را دنبال کنید:

# سرور ایران

داخل سرور ایران به این صورت برنامه رو اجرا میکنیم
```sh
./RTT --iran  --lport:443  --sni:splus.ir --password:123
```

--lport :

میگه که سرور ایران به چه پورتی گوش بده ؛ این پورت بهتره که 443 باشه. کانفیگی که به کاربر میدین باید به ایپی سرور ایران و به همین پورت اشاره کنه ؛ ولی خوب توی پنل میتونید پورت دیگه ای رو براش گذاشته باشین 

--sni:

این همون دامنه ایی هست که میخواهیم باهاش handshake رو کامل کنیم ؛ هر دامنه ای که توی مرورگرتون باز میشه رو میتونید اینجا وارد کنید اما طبیعتا باید دامنه هایی استفاده کنید که مال ایران هستن (نه لزوما .ir ها) و سرورشون داخل ایران هست 
پیدا کردن این sni کار ساده ای هست میشه گفت همه سایت های ایرانی باز هستن به جز یکی دوتا استثنا مثلا snapp.ir برای من جواب نداد ولی سایت های دیگه مثل divar.ir,splus.ir به راحتی و بدون مشکل جواب میدن.

  
--password:
 
این رمز برای رمزنگاری دیتا بین تونل و سرور مقصد استفاده میشه ؛ هرچی دوست دارید وارد کنید اصلا مهم نیست حتی 123 چون ما از hash این پسورد استفاد میکنیم
هر پسوردی که اینجا وارد کردین؛ دقیقا همینو باید در سرور خارج هم وارد کنید وگر نه کار نمیکنه.

# سرور خارج

در سرور خارج برنامه به این شکل اجرا می کنیم
```sh
./RTT --kharej --iran-ip:5.4.3.2 --iran-port:443 --toip:127.0.0.1 --toport:2083 --password:123 --sni:splus.ir
```

اینجا کاملا مشخصه چه مواردی وارد شده ؛ ایپی سرور ایران رو بهش دادیم و پورت سرور ایران (lport تو دستور سرور ایران) و بعد toip که چون ما پنل روی همین سرور خارج نصب کردیم پس میگیم ایپی همین سرور 127.0.0.1
و در نهایت toport که باید همون پورت کانفیگی باشه که پنل بهمون داده ؛ مقادیر password, sni هم دقیقا مثل سرور ایران باشه

دقت کنید که سرور خارج برنامه به پورت خاصی گوش نمیده پس پورت ۴۴۳ هم در اختیار دارید که کانفیگتونو روش بسازید ولی من برای مثال گزاشتم ۲۰۸۳

و بعد از اجرای این دستور باید تانل کار کنه بدون مشکل ؛ لاگ ها هم تا حد خوبی به شما در پیدا کردن مشکل کمک میکنه 


# مشکلات و دیباگ

برای اینکه برنامه بعد از بسته شدن ssh ؛ بسته نشه و ران بمونه از nohup یا [service](https://github.com/radkesvat/ReverseTlsTunnel/blob/master/Service.md) استفاده کنید 

پیشناهاد میکنم از سرویس استفاده کنین چون اگرم برنامه کرش کنه دوباره استارت میشه توسط سرویس و همچنین اگر سرور شما ری استارت بشه هم ؛ برنامه بعد از بوت شدن اجرا میشه

مثلا با nohup :
دستوری که ران کردید رو اولش بنویسید nohup و آخرش یه & قرار بدین که یعنی:
```sh
./RTT --iran  --lport:443  --sni:splus.ir --password:123
```
میشه :
```sh
nohup ./RTT --iran  --lport:443  --sni:splus.ir --password:123 &
```
روی سرور خارج هم همین تغییر رو بدین تا برنامه اجرا بمونه

و وقتی اینشکلی اجرا کنید ؛ برای بستن برنامه ازاین دستور استفاده کنید
> pkill RTT

و برای دیدن لاگ های برنامه از این دستور
> tail -f nohup.out





* * *


اگه با خطای زیر مواجه شدین 
> Resource temporarily unavailable [OSError]

به این معنیه که دامنه ای که زدین داخل sni ؛ نمیتونه ip اش رو پیدا کنه ؛ ۲ تا کار میتونین بکنین یا یه دامنه دیگه قرار بدین و تست کنین یا اینکه dns سرورتونو عوض کنید و بزارین روی یه dns که اون دامنه رو بشناسه




* * *


اگر با خطای زیر مواجه شدین
> Could not increase system max connection (file descriptors) limit.

یا

> sysctl: permission denied on key "fs.file-max"

یعنی دسترسی کامل به root نداریم ؛ مثلا برای کانتینر ها این مشکل هست یا محیط های خاص ؛ اگه داخل سرور واقعی بودین که کافیه از sudo -i استفاده کنین ولی به هر حال اگر موفق نشدین ؛ داخل دستور اجرای برنامه از این سوییچ استفاده کنین
> --keep-os-limit

این باعث میشه که برنامه نتونه محدودیت تعداد کانکشن همزمان که دیفالتش ۱۰۲۴ هست رو بیشتر کنه ولی عوضش کار میکنه

۱۰۲۴ کانکشن یعنی حدودا ۲۰ تا ۳۰ نفر میتونن همزمان وصل باشن ؛ چیز مهمی نیست
* * *
برنامه در هنگام شروع فایروال ufw رو غیرفعال میکنه برای جلوگیری از مشکلات احتمالی چون باید کانفیگش کنید سر هر پورتی و بیشتر دوستان آشنایی ندارن
برای اینکه این اتفاق نیفته ازاین سوییچ استفاده کنید

> --keep-ufw

* * *

بازم اگه مشکلی خوردین و حل نشد issue بزنید تا چک کنیم 

برای این تونل اپدیت  stable شدن Mux در برنامه است...

* * *

اینترنت یا برای همه یا برای هیچکس!

به امید آزادی

