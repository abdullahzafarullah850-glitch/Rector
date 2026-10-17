# Rector
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Global Marketplace</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: Arial, sans-serif; }
        body { background-color: #f4f4f4; color: #333; height: 100vh; display: flex; flex-direction: column; }
        
        .hidden { display: none !important; }
        .card { background: white; padding: 30px; border-radius: 8px; box-shadow: 0 4px 10px rgba(0,0,0,0.1); width: 380px; margin: auto; }
        h2 { text-align: center; color: #002f34; margin-bottom: 15px; }
        input, select, textarea { width: 100%; padding: 10px; margin: 8px 0; border: 1px solid #ccc; border-radius: 4px; }
        button { width: 100%; padding: 10px; background: #002f34; color: white; border: none; border-radius: 4px; font-weight: bold; cursor: pointer; margin-top: 10px; }
        button:hover { background: #004d55; }
        .switch-btn { background: none; color: #002f34; border: none; cursor: pointer; width: 100%; margin-top: 10px; text-decoration: underline; }
        .error { color: #d9534f; font-size: 12px; margin-bottom: 5px; }
        .terms-box { font-size: 11px; color: #666; height: 70px; overflow-y: scroll; border: 1px solid #ddd; padding: 5px; margin: 8px 0; }
        
        #app-container { display: flex; flex-direction: column; height: 100vh; }
        header { background: #002f34; color: white; padding: 15px; display: flex; justify-content: space-between; align-items: center; }
        .search-bar-container { display: flex; justify-content: center; flex: 1; margin: 0 20px; }
        .search-bar-container input { max-width: 500px; margin: 0; border-radius: 4px 0 0 4px; border: none; }
        .search-bar-container select { max-width: 150px; margin: 0; border-radius: 0 4px 4px 0; border: none; background: #fff; }

        .main-body { display: flex; flex: 1; overflow: hidden; position: relative; }
        aside { width: 260px; background: white; padding: 15px; border-right: 1px solid #ddd; overflow-y: auto; }
        aside h3 { font-size: 15px; margin-bottom: 10px; color: #002f34; }
        
        section { flex: 1; padding: 20px; overflow-y: auto; padding-bottom: 90px; }
        .product-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(220px, 1fr)); gap: 15px; }
        .product-card { background: white; border: 1px solid #ddd; border-radius: 6px; padding: 12px; display: flex; flex-direction: column; justify-content: space-between; }
        .product-card h4 { color: #002f34; margin-bottom: 5px; }
        .price { font-weight: bold; color: #b12704; font-size: 16px; margin: 5px 0; }

        .bottom-nav { position: fixed; bottom: 0; left: 0; width: 100%; height: 75px; background: white; border-top: 1px solid #ddd; display: flex; justify-content: space-around; align-items: center; z-index: 1000; padding: 0 20px; }
        .nav-item { background: none; border: none; cursor: pointer; display: flex; flex-direction: column; align-items: center; font-size: 12px; color: #555; width: auto; margin: 0; }
        .nav-item span { font-size: 20px; margin-bottom: 2px; }
        
        .plus-btn { width: 55px; height: 55px; background: #ffce32; color: #002f34; border-radius: 50%; border: none; font-size: 30px; font-weight: bold; display: flex; justify-content: center; align-items: center; cursor: pointer; box-shadow: 0 4px 8px rgba(0,0,0,0.2); position: absolute; bottom: 15px; left: 50%; transform: translateX(-50%); }
        .plus-btn:hover { background: #e0b328; }
        .logout-btn { background-color: #d9534f !important; }
        .logout-btn:hover { background-color: #c9302c !important; }
        
        .settings-section { margin-top: 10px; padding-top: 10px; border-top: 1px solid #eee; font-size: 13px; }
    </style>
</head>
<body>

    <!-- AUTHENTICATION SCREEN -->
    <div class="card" id="auth-container">
        <div id="signup-form">
            <h2>Create Account</h2>
            <div id="signup-error" class="error"></div>
            <input type="email" id="signup-email" placeholder="Enter Email" required>
            <input type="password" id="signup-password" placeholder="Password (Min 6 chars, letters & symbols)" required>
            
            <label style="font-size: 12px; font-weight: bold;">Select Your Country:</label>
            <select id="signup-country">
                <option value="Pakistan">Pakistan</option>
                <option value="USA">United States</option>
                <option value="UK">United Kingdom</option>
                <option value="UAE">United Arab Emirates</option>
                <option value="Canada">Canada</option>
                <option value="Australia">Australia</option>
            </select>

            <div class="terms-box">
                <strong>OLX-Style Terms & Conditions:</strong><br>
                1. Responsible use only. No prohibited items.<br>
                2. Marketplace acts as a venue; transactions are between users.<br>
                3. Privacy protected per our security guidelines.
            </div>
            <label style="font-size: 12px;">
                <input type="checkbox" id="terms-checkbox"> I agree to Terms & Conditions
            </label>

            <button onclick="handleSignUp()">Sign Up</button>
            <button class="switch-btn" onclick="switchView('login')">Already have an account? Log In</button>
        </div>

        <div id="login-form" class="hidden">
            <h2>Log In</h2>
            <div id="login-error" class="error"></div>
            <input type="email" id="login-email" placeholder="Enter Email" required>
            <input type="password" id="login-password" placeholder="Enter Password" required>
            <button onclick="handleLogin()">Log In</button>
            <button class="switch-btn" onclick="switchView('signup')">Don't have an account? Sign Up</button>
        </div>
    </div>

    <!-- MAIN APP INTERFACE -->
    <div id="app-container" class="hidden">
        <header>
            <div><strong>GlobalMarket</strong></div>
            <div class="search-bar-container">
                <input type="text" id="search-input" placeholder="Search vehicles, mobiles, laptops, spare parts..." onkeyup="filterAds()">
                <select id="scope-select" onchange="filterAds()">
                    <option value="local">My Country Only</option>
                    <option value="international">🌍 International (All)</option>
                </select>
            </div>
            <div><span id="user-country-badge" style="background:#004d55; padding: 5px 10px; border-radius: 4px; font-size: 12px;"></span></div>
        </header>

        <div class="main-body">
            <aside>
                <h3>Filters</h3>
                <label style="font-size: 12px;">Category:</label>
                <select id="filter-category" onchange="filterAds()">
                    <option value="All">All Categories</option>
                    <option value="Vehicles">Vehicles</option>
                    <option value="Mobiles">Mobiles</option>
                    <option value="Laptops">Laptops</option>
                    <option value="Electronics">Electronics</option>
                    <option value="Spare Parts">Spare Parts</option>
                    <option value="Shops">Shops / Businesses</option>
                </select>

                <label style="font-size: 12px; margin-top: 10px; display: block;">Condition:</label>
                <select id="filter-condition" onchange="filterAds()">
                    <option value="All">Any Condition</option>
                    <option value="New">New</option>
                    <option value="Used">Used</option>
                </select>

                <label style="font-size: 12px; margin-top: 10px; display: block;">Max Price ($):</label>
                <input type="number" id="filter-price" placeholder="e.g. 5000" onkeyup="filterAds()" onchange="filterAds()">
            </aside>

            <section>
                <h2 style="text-align: left; font-size: 20px; margin-bottom: 15px;">Marketplace Listings</h2>
                <div class="product-grid" id="ads-grid"></div>
            </section>
        </div>

        <nav class="bottom-nav">
            <button class="nav-item" onclick="openChat()"><span>💬</span> Chat Area</button>
            <button class="plus-btn" onclick="openPostModal()">+</button>
            <button class="nav-item" onclick="openProfile()"><span>👤</span> Profile</button>
        </nav>
    </div>

    <!-- POST AD MODAL -->
    <div class="card hidden" id="post-modal" style="position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%); z-index: 2000; width: 400px; max-height: 90vh; overflow-y: auto;">
        <h2>Post New Ad / Product</h2>
        <input type="text" id="post-title" placeholder="Product Title (e.g. iPhone 14 / Civic Car)">
        <select id="post-category">
            <option value="Vehicles">Vehicles</option>
            <option value="Mobiles">Mobiles</option>
            <option value="Laptops">Laptops</option>
            <option value="Electronics">Electronics</option>
            <option value="Spare Parts">Spare Parts</option>
            <option value="Shops">Shop / Business</option>
        </select>
        <input type="text" id="post-brand" placeholder="Brand Name (e.g. Apple, Honda, Dell)">
        <select id="post-condition">
            <option value="New">New</option>
            <option value="Used">Used</option>
        </select>
        <input type="number" id="post-price" placeholder="Price (in USD)">
        <input type="number" id="post-shipping" placeholder="International Shipping Cost ($)">
        
        <label style="font-size: 12px; margin-top: 5px; display: block; font-weight: bold;">Accepted Payment Method:</label>
        <select id="post-payment-method">
            <option value="Cash on Delivery (COD)">Cash on Delivery (COD)</option>
            <option value="Online Payment">Online Payment (Credit Card / PayPal)</option>
            <option value="Both COD & Online Payment">Both COD & Online Payment</option>
        </select>

        <textarea id="post-desc" placeholder="Detailed Description..."></textarea>
        <button onclick="submitAd()">Publish Ad</button>
        <button class="switch-btn" onclick="closePostModal()">Cancel</button>
    </div>

    <!-- PROFILE MODAL WITH OLX SETTINGS -->
    <div class="card hidden" id="profile-modal" style="position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%); z-index: 2000; width: 420px; max-height: 90vh; overflow-y: auto;">
        <h2>User Profile & Settings</h2>
        <p><strong>Email:</strong> <span id="prof-email"></span></p>
        <p><strong>Country:</strong> <span id="prof-country"></span></p>
        
        <h3 style="font-size: 14px; margin-top: 12px; color:#002f34;">Your Posted Ads:</h3>
        <div id="user-ads-list" style="max-height: 100px; overflow-y: auto; font-size: 13px; border: 1px solid #ddd; padding: 5px; margin-bottom: 10px;"></div>

        <!-- OLX Settings Section -->
        <div class="settings-section">
            <h3 style="font-size: 14px; color:#002f34; margin-bottom: 6px;">⚙️ OLX Account Settings</h3>
            <label style="font-size: 12px; display: block; margin-top: 5px;">
                <input type="checkbox" id="setting-phone-privacy" checked> Show my phone number in ads
            </label>
            <label style="font-size: 12px; display: block; margin-top: 5px;">
                <input type="checkbox" id="setting-notifications" checked> Receive push & email notifications
            </label>
            <button style="background: #555; margin-top: 8px; padding: 6px; font-size: 12px;" onclick="alert('Settings saved successfully!')">Save Settings</button>
            <button style="background: #d9534f; margin-top: 6px; padding: 6px; font-size: 12px;" onclick="deactivateAccount()">Deactivate Account</button>
        </div>

        <button class="logout-btn" style="margin-top: 15px;" onclick="handleLogout()">Log Out</button>
        <button class="switch-btn" onclick="closeProfile()">Close</button>
    </div>

    <!-- CHAT AREA MODAL -->
    <div class="card hidden" id="chat-modal" style="position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%); z-index: 2000; width: 400px;">
        <h2>Chat Area</h2>
        <div style="height: 150px; border: 1px solid #ddd; padding: 8px; overflow-y: auto; font-size: 13px; background: #fafafa; margin-bottom: 8px;">
            <p><strong>Buyer/Seller Support:</strong> Discuss pricing, condition, and shipping details safely here.</p>
        </div>
        <input type="text" id="chat-msg-input" placeholder="Type a message...">
        <button onclick="alert('Message sent to user!')">Send Message</button>
        <button class="switch-btn" onclick="closeChat()">Close</button>
    </div>

    <script>
        let ads = JSON.parse(localStorage.getItem('marketplaceAds')) || [
            { id: 1, title: "Honda Civic 2022", category: "Vehicles", brand: "Honda", condition: "Used", price: 22000, shipping: 1500, paymentMethod: "Cash on Delivery (COD)", country: "Pakistan", seller: "seller1@test.com", desc: "Mint condition vehicle." },
            { id: 2, title: "MacBook Pro M2", category: "Laptops", brand: "Apple", condition: "New", price: 1299, shipping: 50, paymentMethod: "Online Payment", country: "USA", seller: "seller2@test.com", desc: "Brand new sealed laptop." }
        ];

        window.onload = function() {
            const currentUser = localStorage.getItem('currentUser');
            if (currentUser) {
                activateApp(currentUser);
            }
        };

        function switchView(view) {
            if (view === 'login') {
                document.getElementById('signup-form').classList.add('hidden');
                document.getElementById('login-form').classList.remove('hidden');
            } else {
                document.getElementById('login-form').classList.add('hidden');
                document.getElementById('signup-form').classList.remove('hidden');
            }
        }

        function handleSignUp() {
            const email = document.getElementById('signup-email').value;
            const password = document.getElementById('signup-password').value;
            const country = document.getElementById('signup-country').value;
            const termsChecked = document.getElementById('terms-checkbox').checked;
            const errorDiv = document.getElementById('signup-error');

            const minLength = password.length >= 6;
            const hasLetter = /[a-zA-Z]/.test(password);
            const hasSymbol = /[!@#$%^&*(),.?":{}|<>]/.test(password);

            if (!email || !password) { errorDiv.innerText = "Fill in all fields."; return; }
            if (!minLength || !hasLetter || !hasSymbol) { errorDiv.innerText = "Password must be 6+ chars with letters & symbols."; return; }
            if (!termsChecked) { errorDiv.innerText = "You must agree to Terms & Conditions."; return; }

            localStorage.setItem('savedEmail', email);
            localStorage.setItem('savedPassword', password);
            localStorage.setItem('savedCountry', country);
            localStorage.setItem('currentUser', email);

            errorDiv.innerText = "";
            activateApp(email);
        }

        function handleLogin() {
            const email = document.getElementById('login-email').value;
            const password = document.getElementById('login-password').value;
            const errorDiv = document.getElementById('login-error');

            if (email === localStorage.getItem('savedEmail') && password === localStorage.getItem('savedPassword')) {
                localStorage.setItem('currentUser', email);
                errorDiv.innerText = "";
                activateApp(email);
            } else {
                errorDiv.innerText = "Invalid credentials.";
            }
        }

        function activateApp(email) {
            document.getElementById('auth-container').classList.add('hidden');
            document.getElementById('app-container').classList.remove('hidden');
            document.getElementById('user-country-badge').innerText = "Country: " + (localStorage.getItem('savedCountry') || "Pakistan");
            filterAds();
        }

        function filterAds() {
            const query = document.getElementById('search-input').value.toLowerCase();
            const scope = document.getElementById('scope-select').value;
            const userCountry = localStorage.getItem('savedCountry');
            const category = document.getElementById('filter-category').value;
            const condition = document.getElementById('filter-condition').value;
            const maxPrice = parseFloat(document.getElementById('filter-price').value) || Infinity;

            const grid = document.getElementById('ads-grid');
            grid.innerHTML = "";

            const filtered = ads.filter(ad => {
                const matchesScope = (scope === 'international') || (ad.country === userCountry);
                const matchesSearch = ad.title.toLowerCase().includes(query) || ad.brand.toLowerCase().includes(query);
                const matchesCat = (category === 'All' || ad.category === category);
                const matchesCond = (condition === 'All' || ad.condition === condition);
                const matchesPrice = ad.price <= maxPrice;
                return matchesScope && matchesSearch && matchesCat && matchesCond && matchesPrice;
            });

            if (filtered.length === 0) {
                grid.innerHTML = "<p>No matching listings found.</p>";
                return;
            }

            filtered.forEach(ad => {
                const isInternational = ad.country !== userCountry;
                const totalCost = isInternational ? (ad.price + ad.shipping) : ad.price;
                
                const card = document.createElement('div');
                card.className = 'product-card';
                card.innerHTML = `
                    <div>
                        <span style="font-size: 10px; background: #eee; padding: 2px 5px; border-radius: 3px;">${ad.country} ${isInternational ? '(Intl)' : ''}</span>
                        <h4>${ad.title}</h4>
                        <p style="font-size: 12px; color: #666;">Brand: ${ad.brand} | ${ad.condition}</p>
                        <div class="price">$${ad.price}</div>
                        ${isInternational ? `<p style="font-size: 11px; color: #444;">+ Shipping: $${ad.shipping} (Total:$${totalCost})</p>` : ''}
                        <p style="font-size: 11px; margin-top: 4px;"><strong>Payment Accepted:</strong> ${ad.paymentMethod}</p>
                    </div>
                    <button onclick="alert('Order placed successfully using ${ad.paymentMethod}!')">Buy / Order Now</button>
                `;
                grid.appendChild(card);
            });
        }

        function openPostModal() { document.getElementById('post-modal').classList.remove('hidden'); }
        function closePostModal() { document.getElementById('post-modal').classList.add('hidden'); }

        function submitAd() {
            const title = document.getElementById('post-title').value;
            const category = document.getElementById('post-category').value;
            const brand = document.getElementById('post-brand').value;
            const condition = document.getElementById('post-condition').value;
            const price = parseFloat(document.getElementById('post-price').value);
            const shipping = parseFloat(document.getElementById('post-shipping').value) || 0;
            const paymentMethod = document.getElementById('post-payment-method').value;
            const desc = document.getElementById('post-desc').value;
            const userCountry = localStorage.getItem('savedCountry');
            const currentUser = localStorage.getItem('currentUser');

            if (!title || !price) { alert("Please fill in the required fields."); return; }

            const newAd = { id: Date.now(), title, category, brand, condition, price, shipping, paymentMethod, country: userCountry, seller: currentUser, desc };
            ads.push(newAd);
            localStorage.setItem('marketplaceAds', JSON.stringify(ads));

            closePostModal();
            filterAds();
            alert("Your ad has been successfully posted!");
        }

        function openProfile() {
            document.getElementById('prof-email').innerText = localStorage.getItem('currentUser');
            document.getElementById('prof-country').innerText = localStorage.getItem('savedCountry');
            
            const userAds = ads.filter(ad => ad.seller === localStorage.getItem('currentUser'));
            const listDiv = document.getElementById('user-ads-list');
            listDiv.innerHTML = userAds.length ? userAds.map(a => `<div>• ${a.title} ($${a.price})</div>`).join('') : "No ads posted yet.";

            document.getElementById('profile-modal').classList.remove('hidden');
        }
        function closeProfile() { document.getElementById('profile-modal').classList.add('hidden'); }

        function openChat() { document.getElementById('chat-modal').classList.remove('hidden'); }
        function closeChat() { document.getElementById('chat-modal').classList.add('hidden'); }

        function deactivateAccount() {
            if(confirm("Are you sure you want to deactivate your account?")) {
                localStorage.clear();
                alert("Account deactivated.");
                location.reload();
            }
        }

        function handleLogout() {
            localStorage.removeItem('currentUser');
            location.reload();
        }
    </script>
</body>
</html>
