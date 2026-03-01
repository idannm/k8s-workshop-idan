‫Gemini אמר
הבנתי אותך לגמרי, עידן. קובץ README מקצועי זה כרטיס הביקור של הפרויקט שלך. המרצה רוצה לקרוא אותו ולהגיד "וואו, הבחור הזה מבין את הארכיטקטורה של המערכת, הוא לא סתם הריץ פקודות".

הכנתי לך קובץ README.md חדש, משודרג ומרשים. יש בו הסבר על כל רכיב, למה בחרת בו, ומה הוא עושה.

פשוט תעתיק את כל הטקסט שנמצא בתוך התיבה השחורה פה למטה, ותדביק אותו בקובץ ה-README.md שלך ב-GitHub במקום מה שיש שם עכשיו:

Markdown
# 🚀 K8s Workshop Final Project

## 📌 אודות הפרויקט (Project Overview)
פרויקט זה מציג סביבת קוברנטיס (Kubernetes) מלאה ועובדת, הכוללת פריסה של אתר WordPress המחובר למסד נתונים MySQL. 
במקום לפרוס את המשאבים ידנית, הפרויקט כולו ארוז ומנוהל באמצעות **Helm Chart** ייעודי (`idan-chart`), המאפשר התקנה, ניהול ושדרוג של כל הארכיטקטורה בפקודה אחת פשוטה. בנוסף, הסביבה כוללת מערכת ניטור מתקדמת מבוססת Prometheus ו-Grafana.

---

## 🏗️ ארכיטקטורת המערכת (System Architecture)
הפרויקט מורכב ממספר רכיבי תשתית מרכזיים כדי להבטיח זמינות, אבטחה ושמירת נתונים:

1. **WordPress (Frontend / Application):**
   * מנוהל כ-`Deployment` כדי לאפשר גמישות (Scale) עתידית.
   * נגיש דרך `ClusterIP Service` פנימי לאבטחת התקשורת.

2. **MySQL (Database):**
   * מנוהל כ-`StatefulSet` ולא כ-Deployment רגיל, מכיוון שמסד נתונים דורש זהות יציבה.
   * מחובר ל-**Persistent Volume Claim (PVC)** המבטיח שהמידע של האתר (פוסטים, משתמשים, הגדרות) יישמר לתמיד, גם אם הפוד קורס או עובר שרת.
   * התקשורת מול הוורדפרס מתבצעת דרך **Headless Service** (ללא IP כללי), כדי שהאפליקציה תתחבר ישירות לדיסק הנכון ללא Load Balancing שמפריע ל-DB.

3. **NGINX Ingress Controller (Gateway):**
   * משמש כ"שוער" של הקלאסטר. מקבל את כל התעבורה החיצונית מהאינטרנט ומנתב אותה פנימה בצורה בטוחה וחכמה ל-Service של הוורדפרס.

4. **Monitoring (מערכת ניטור):**
   * שימוש ב-`kube-prometheus-stack` כדי לאסוף מדדים (Metrics) מהקלאסטר בזמן אמת.
   * לוחות בקרה (Dashboards) ב-Grafana לניטור בריאות השרת (CPU, Memory, Disk) ומשאבי הקוברנטיס.

---

## ⚙️ דרישות מוקדמות (Prerequisites)
* שרת Minikube פעיל (רץ על מכונת EC2).
* כלי שורת הפקודה: `kubectl`, `helm`.

---

## 🛠️ הוראות התקנה (Installation Guide)

### שלב 1: התקנת נתב התעבורה (NGINX Ingress Controller)
כדי לחשוף את האתר לעולם, נתקין תחילה את ה-Ingress Controller:
```bash
helm install ingress-nginx ingress-nginx/ingress-nginx --create-namespace --namespace ingress-nginx
שלב 2: פריסת האפליקציה ומסד הנתונים (הפרויקט שלנו)
התקנת ה-Helm Chart שיצרנו (idan-chart), אשר מרים אוטומטית את ה-WordPress, ה-MySQL (עם ה-PVC) ואת שאר משאבי הרשת:

Bash
helm install my-site ./idan-chart
שלב 3: התקנת מערכת הניטור (Prometheus & Grafana)
הקמת סביבת הניטור ב-Namespace נפרד כדי לעקוב אחרי ביצועי המערכת:

Bash
helm install monitoring prometheus-community/kube-prometheus-stack --namespace monitoring --create-namespace
📊 גישה למערכת הניטור (Accessing Grafana)
כדי לצפות בגרפים ובמדדי השרת בזמן אמת, יש לפתוח ערוץ תקשורת מאובטח (Port-Forward) לתוך הקלאסטר:

פתיחת הגישה:

Bash
kubectl port-forward -n monitoring svc/monitoring-grafana 3000:80 --address 0.0.0.0 &
כניסה לדפדפן:
פתחו את הכתובת: http://<YOUR-EC2-IP>:3000

פרטי התחברות:

שם משתמש: admin

סיסמה: (הריצו את הפקודה הבאה בטרמינל כדי לשלוף את הסיסמה המוצפנת מתוך ה-Secrets של קוברנטיס):

Bash
kubectl get secret --namespace monitoring monitoring-grafana -o jsonpath="{.data.a
