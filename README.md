<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>موقع الزبون</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            color: #333;
            text-align: center;
            padding: 50px;
        }
        h1 {
            color: #007bff;
        }
        input, button {
            padding: 10px;
            margin: 10px;
            font-size: 1em;
        }
        .order-status {
            background-color: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            display: inline-block;
            margin-top: 20px;
        }
        .status {
            font-size: 1.2em;
            font-weight: bold;
        }
        .status.accepted {
            color: #28a745;
        }
        .status.rejected {
            color: #dc3545;
        }
    </style>
</head>
<body>
    <h1>أضف طلب جديد</h1>
    <input type="text" id="customerName" placeholder="اسمك">
    <input type="text" id="orderDetails" placeholder="تفاصيل الطلب">
    <button onclick="addOrder()">أضف الطلب</button>

    <div class="order-status">
        <p>حالة الطلب: <span id="status" class="status">لا يوجد طلبات</span></p>
    </div>

    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/10.1.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.1.0/firebase-database.js"></script>

    <script>
        // Firebase Config (استبدل الكود ده بالكود الخاص بمشروعك)
        const firebaseConfig = {
            apiKey: "AIzaSyDxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
            authDomain: "your-project-id.firebaseapp.com",
            databaseURL: "https://your-project-id.firebaseio.com",
            projectId: "your-project-id",
            storageBucket: "your-project-id.appspot.com",
            messagingSenderId: "1234567890",
            appId: "1:1234567890:web:xxxxxxxxxxxxxxxxxxxxxx"
        };

        // Initialize Firebase
        const app = firebase.initializeApp(firebaseConfig);
        const database = firebase.database();

        // نضيف طلب جديد
        function addOrder() {
            const customerName = document.getElementById('customerName').value;
            const orderDetails = document.getElementById('orderDetails').value;

            if (!customerName || !orderDetails) {
                alert('من فضلك املأ كل الحقول!');
                return;
            }

            // ننشئ الطلب الجديد
            const newOrder = {
                id: Date.now(), // نستخدم الوقت علشان يكون ID فريد
                customerName: customerName,
                orderDetails: orderDetails,
                status: 'قيد الانتظار'
            };

            // نرسل الطلب لـ Firebase
            database.ref('orders/' + newOrder.id).set(newOrder)
                .then(() => {
                    alert('تم إضافة الطلب بنجاح!');
                    document.getElementById('customerName').value = '';
                    document.getElementById('orderDetails').value = '';
                })
                .catch((error) => {
                    alert('حدث خطأ: ' + error.message);
                });
        }

        // نستمع للتحديثات على الطلب
        database.ref('orders').on('value', (snapshot) => {
            const orders = snapshot.val();
            const lastOrder = orders ? Object.values(orders).pop() : null;
            const statusElement = document.getElementById('status');

            if (lastOrder) {
                statusElement.textContent = lastOrder.status;

                if (lastOrder.status === 'تم القبول') {
                    statusElement.classList.add('accepted');
                    statusElement.classList.remove('rejected');
                } else if (lastOrder.status === 'تم الرفض') {
                    statusElement.classList.add('rejected');
                    statusElement.classList.remove('accepted');
                } else {
                    statusElement.classList.remove('accepted', 'rejected');
                }
            } else {
                statusElement.textContent = 'لا يوجد طلبات';
            }
        });
    </script>
</body>
</html>
