# دليل AeoScript باللغة العربية

[الصفحة الرئيسية](README.md) · [توثيق اللغة الرسمي](https://aeowun.com/aeoscript/) · [مرجع API](https://aeowun.com/docs/reference/)

تُستخدم لغة **AeoScript** لكتابة منطق اللعب في Worldkiln. الشروح هنا بالعربية، لكن **أسماء الدوال والمتغيرات والكلمات المحجوزة في الأمثلة بقيت باللغة الأصلية**.

> هذه ترجمة بمساعدة الذكاء الاصطناعي ولم تُراجع بعد من متخصص يتحدث العربية. يُرجى [إرسال طلب سحب](CONTRIBUTING.md) لتصحيح أي خطأ. تحقّق من [التوثيق الإنجليزي الأصلي](https://github.com/Aeowun/Worldkiln/blob/master/AEOSCRIPT.md) عند وجود اختلاف.

## مثال على حدث داخل اللعبة

أنشئ الملف `scripts/score.aeo` في مشروع Worldkiln:

```aeoscript
score: number = 0

on coin_collected(points) {
    score += points
    debug.log("Score:", score)
}

event.fire("coin_collected", 10)
```

ابدأ وضع **Play** ثم افتح نافذة **Output** لمشاهدة النتيجة. يستقبل معالج الحدث قيمةً، ويضيفها إلى النقاط، ثم يكتبها في السجل.

## المتغيرات والدوال

استخدم `const` لتعريف قيمة ثابتة، واسم متغير متبوعًا بنوعه لتعريف متغير قابل للتغيير. يغيّر الإسناد العادي قيمة متغير موجود.

```aeoscript
const BONUS = 5
total: number = 10

fn award(points) {
    total += points + BONUS
}

award(10)
```

تدعم AeoScript كذلك الدوال والإغلاقات والمجموعات والتحكم في سير التنفيذ و`wait()`. للمواصفات الدقيقة: [القواعد](https://aeowun.com/aeoscript/syntax/)، [الدوال](https://aeowun.com/aeoscript/functions/)، و[الانتظار](https://aeowun.com/aeoscript/waiting/).

## البرامج النصية والوحدات والكيانات

| النوع | الاستخدام |
| --- | --- |
| Project script | أحداث اللعبة العامة والتهيئة والأنظمة المشتركة |
| Module | إعادة استخدام الدوال والحالة، بما في ذلك السلوك المرفق بـ Actor |
| Entity script | حقول الكائن ودوال دورة حياته |

يمكن الإشارة إلى وحدة أخرى كما يلي:

```aeoscript
const helper = scripts.game_helpers.aeo
```

ويمكن للكيان الاحتفاظ بحالته الخاصة:

```aeoscript
entity Counter {
    count: number = 0

    fn update(dt) {
        count += 1
    }
}
```

راجع [Modules](https://aeowun.com/aeoscript/modules/) و[Entity scripts](https://aeowun.com/aeoscript/entities/).

## التعامل مع كائنات Worldkiln

ابحث عن Actor باستخدام اسمه:

```aeoscript
const door = proj.find("Door")

if door != nil {
    debug.log(door)
}
```

يمكنك استكشاف واجهات [البحث](https://aeowun.com/aeoscript/find/) و[الإدخال](https://aeowun.com/aeoscript/input/) و[الواجهة](https://aeowun.com/aeoscript/ui/) و[الصوت](https://aeowun.com/aeoscript/audio/) و[الأحداث](https://aeowun.com/aeoscript/events/).

## حفظ حالة اللعبة

عادةً تُعاد التغييرات التي تحدث أثناء التشغيل عند إنهاء وضع Play. لحفظ القيم التي يجب أن تبقى، استخدم واجهات `save`:

```aeoscript
save.set("coins", 20)
save.commit()
```

استرجع القيمة باستخدام `save.get("coins")`. اقرأ [دليل حفظ الحالة](https://aeowun.com/aeoscript/save/) لفهم توقيت الحفظ والاسترجاع.

## دورة حياة اللعبة

| الواجهة | الوظيفة |
| --- | --- |
| `game.quit()` | إرسال إشارة إنهاء اللعبة ثم الخروج من نسخة اللعبة الحالية أو مغادرة وضع Play |
| `game.restart()` | إعادة تحميل جلسة اللعبة في النافذة نفسها، مع الاحتفاظ بالقيم المحفوظة |
| `game.pause()` | إيقاف المحاكاة مؤقتًا في اللعب الفردي؛ لا يوقف جلسة تعدد اللاعبين |
| `game.resume()` | استئناف محاكاة اللعب الفردي |
| `game.is_paused` | قيمة منطقية تشير إلى الإيقاف المؤقت |
| `game.elapsed_time` | ثواني اللعب النشط منذ بدء الجلسة |
| `game.delta_time` | مقدار التقدم الزمني في آخر تحديث |
| `game.is_editor` | صواب عند التشغيل داخل المحرّر |
| `game.is_standalone` | صواب عند التشغيل في نافذة لعبة مستقلة |
| `game.on_quit` | اسم الحدث `game_on_quit` |
| `game.on_pause` | اسم الحدث `game_on_pause` |
| `game.on_resume` | اسم الحدث `game_on_resume` |

مثال للاستجابة لطلب إنهاء اللعبة:

```aeoscript
on game_on_quit() {
    save.commit()
}
```

يُرسل `game_on_quit` إشارة فقط؛ أما عمليات الحفظ والتصرفات الأخرى فتُحددها أنت في الشيفرة. ولا تؤدي `save.restart()` وحدها إلى إعادة تشغيل اللعبة؛ بل تتعامل مع حالة الحفظ.

## دعوات تعدد اللاعبين عبر الإنترنت (قيد التطوير في V1.1.0)

تصف الوثائق الأصلية هذه الواجهات باعتبارها جزءًا من تطوير V1.1.0؛ لذا تحقّق من الإصدار المثبّت قبل استخدامها.

| الواجهة | الاستخدام |
| --- | --- |
| `network.host_invite(2)` | طلب إنشاء جلسة مستضافة عبر وسيط اتصال؛ السعة الاختيارية من 2 إلى 8 |
| `network.join_invite(link)` | الانضمام عبر رابط HTTPS أو رمز دعوة مكوّن من 32 حرفًا |
| `network.invite_link()` | الحصول على رابط دعوة المضيف، إن كان متاحًا |
| `network.leave()` | مغادرة الجلسة والعودة إلى نمط Solo |
| `clipboard.write(link)` | نسخ الرابط إلى الحافظة |
| `clipboard.read()` | قراءة النص من الحافظة |

```aeoscript
on network_invite_ready(link) {
    clipboard.write(link)
    debug.log("Invitation copied")
}

on network_join_failed(reason) {
    debug.log("Unable to join:", reason)
}
```

يُصدر المضيف الحدث `network_invite_ready(link)` عند تجهيز الدعوة، ويُستخدم `network_join_failed(reason)` للإبلاغ عن فشل الانضمام. لا ينبغي افتراض توافر هذه الواجهات في جميع الإصدارات.

## المرجع

للأسماء الدقيقة للواجهات والمعلمات والقيم المعادة، راجع [المرجع الرسمي](https://aeowun.com/docs/reference/). ولمشكلات الشيفرة راجع [التشخيص](https://aeowun.com/aeoscript/diagnostics/).
