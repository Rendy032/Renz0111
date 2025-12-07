<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="id" lang="id">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Toko Baju Muslim</title>
    <style type="text/css">
        body { font-family: Arial, sans-serif; margin: 20px; }
        .auth { margin-bottom: 20px; }
        .auth input { margin: 5px; padding: 5px; }
        .auth button { padding: 5px 10px; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        th, td { border: 1px solid #ddd; padding: 8px; text-align: center; }
        img { width: 100px; height: 100px; }
        .cart { margin-top: 20px; }
        .cart ul { list-style: none; }
        .cart li { margin: 5px 0; }
        .hidden { display: none; }
    </style>
</head>
<body>
    <h1>Toko Baju Muslim</h1>
    
    <!-- Bagian Auth (Register/Login/Logout) -->
    <div class="auth">
        <div id="registerForm">
            <h2>Register</h2>
            <input type="text" id="regUsername" placeholder="Username" />
            <input type="password" id="regPassword" placeholder="Password" />
            <button onclick="register()">Register</button>
        </div>
        
        <div id="loginForm">
            <h2>Login</h2>
            <input type="text" id="loginUsername" placeholder="Username" />
            <input type="password" id="loginPassword" placeholder="Password" />
            <button onclick="login()">Login</button>
        </div>
        
        <div id="logoutBtn" class="hidden">
            <button onclick="logout()">Logout</button>
        </div>
    </div>
    
    <!-- Tabel Produk -->
    <h2>Daftar Produk</h2>
    <table id="productTable">
        <thead>
            <tr>
                <th>Gambar</th>
                <th>Nama</th>
                <th>Harga</th>
                <th>Aksi</th>
            </tr>
        </thead>
        <tbody>
            <!-- Produk akan dimuat oleh JS -->
        </tbody>
    </table>
    
    <!-- Keranjang Belanja -->
    <div class="cart">
        <h2>Keranjang Belanja</h2>
        <ul id="cartList"></ul>
        <p>Total: <span id="totalPrice">0</span> IDR</p>
    </div>
    
    <script type="text/javascript">
        // Data produk (bisa diganti dengan data dari API atau database)
        const products = [
            { id: 1, name: 'Hijab Segi Empat', price: 50000, image: 'https://picsum.photos/100?random=1' },
            { id: 2, name: 'Abaya Panjang', price: 150000, image: 'https://picsum.photos/100?random=2' },
            { id: 3, name: 'Khimar Bergo', price: 75000, image: 'https://picsum.photos/100?random=3' },
            { id: 4, name: 'Gamis Wanita', price: 200000, image: 'https://picsum.photos/100?random=4' }
        ];
        
        let users = JSON.parse(localStorage.getItem('users')) || [];
        let currentUser = localStorage.getItem('currentUser');
        let cart = JSON.parse(localStorage.getItem('cart')) || [];
        
        // Fungsi untuk menampilkan produk di tabel
        function displayProducts() {
            const tbody = document.querySelector('#productTable tbody');
            tbody.innerHTML = '';
            products.forEach(product => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td><img src="${product.image}" alt="${product.name}" /></td>
                    <td>${product.name}</td>
                    <td>${product.price} IDR</td>
                    <td><button onclick="addToCart(${product.id})">Masukkan Keranjang</button></td>
                `;
                tbody.appendChild(row);
            });
        }
        
        // Fungsi untuk menampilkan keranjang
        function displayCart() {
            const cartList = document.getElementById('cartList');
            cartList.innerHTML = '';
            let total = 0;
            cart.forEach((item, index) => {
                const li = document.createElement('li');
                li.innerHTML = `${item.name} - ${item.price} IDR <button onclick="removeFromCart(${index})">Hapus</button>`;
                cartList.appendChild(li);
                total += item.price;
            });
            document.getElementById('totalPrice').textContent = total;
            localStorage.setItem('cart', JSON.stringify(cart));
        }
        
        // Fungsi register
        function register() {
            const username = document.getElementById('regUsername').value;
            const password = document.getElementById('regPassword').value;
            if (username && password) {
                if (users.find(user => user.username === username)) {
                    alert('Username sudah ada!');
                    return;
                }
                users.push({ username, password });
                localStorage.setItem('users', JSON.stringify(users));
                alert('Registrasi berhasil! Silakan login.');
                document.getElementById('regUsername').value = '';
                document.getElementById('regPassword').value = '';
            } else {
                alert('Isi semua field!');
            }
        }
        
        // Fungsi login
        function login() {
            const username = document.getElementById('loginUsername').value;
            const password = document.getElementById('loginPassword').value;
            const user = users.find(u => u.username === username && u.password === password);
            if (user) {
                currentUser = username;
                localStorage.setItem('currentUser', currentUser);
                alert('Login berhasil!');
                updateUI();
            } else {
                alert('Username atau password salah!');
            }
        }
        
        // Fungsi logout
        function logout() {
            currentUser = null;
            localStorage.removeItem('currentUser');
            updateUI();
        }
        
        // Fungsi add to cart
        function addToCart(productId) {
            if (!currentUser) {
                alert('Silakan login terlebih dahulu!');
                return;
            }
            const product = products.find(p => p.id === productId);
            cart.push(product);
            displayCart();
        }
        
        // Fungsi remove from cart
        function removeFromCart(index) {
            cart.splice(index, 1);
            displayCart();
        }
        
        // Update UI berdasarkan status login
        function updateUI() {
            if (currentUser) {
                document.getElementById('registerForm').classList.add('hidden');
                document.getElementById('loginForm').classList.add('hidden');
                document.getElementById('logoutBtn').classList.remove('hidden');
            } else {
                document.getElementById('registerForm').classList.remove('hidden');
                document.getElementById('loginForm').classList.remove('hidden');
                document.getElementById('logoutBtn').classList.add('hidden');
            }
        }
        
        // Inisialisasi
        displayProducts();
        displayCart();
        updateUI();
    </script>
</body>
</html>
