<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>علي إكسبرس - واجهة متقدمة للتصفية والبحث</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;700&display=swap" rel="stylesheet">
    
    <style>
        /* إعادة الضبط والخطوط */
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { font-family: 'Cairo', sans-serif; background-color: #f7f7f7; color: #333; direction: rtl; }
        a { text-decoration: none; color: inherit; }

        /* شريط التنقل */
        .navbar { background-color: #ff471a; /* لون علي إكسبرس */ color: white; padding: 15px 30px; display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 1000; box-shadow: 0 3px 6px rgba(0, 0, 0, 0.1); }
        .logo a { font-size: 28px; font-weight: 700; color: white; }
        .search-bar { display: flex; flex-grow: 1; margin: 0 40px; max-width: 800px; position: relative; }
        .search-bar input { padding: 10px 15px; border: none; flex-grow: 1; border-radius: 4px 0 0 4px; font-size: 16px; }
        .search-bar button { background-color: #000; color: white; border: none; padding: 10px 15px; cursor: pointer; border-radius: 0 4px 4px 0; }
        .nav-links a { margin-right: 25px; padding: 5px 0; transition: color 0.3s; font-size: 15px; }

        /* اقتراحات البحث */
        .autocomplete-results { position: absolute; top: 100%; right: 0; width: 90%; background: white; border: 1px solid #ccc; border-top: none; z-index: 900; }
        .autocomplete-results div { padding: 10px; cursor: pointer; color: #333; }
        .autocomplete-results div:hover { background-color: #eee; }

        /* هيكل الصفحة الرئيسي */
        .main-content { display: flex; max-width: 1400px; margin: 20px auto; padding: 0 20px; }
        
        /* العمود الجانبي (Filters) */
        .sidebar { width: 280px; padding: 20px; background: white; border-radius: 8px; box-shadow: 0 0 10px rgba(0, 0, 0, 0.05); margin-left: 20px; }
        .filter-group { margin-bottom: 20px; border-bottom: 1px solid #eee; padding-bottom: 15px; }
        .filter-group h3 { font-size: 18px; margin-bottom: 10px; color: #ff471a; }
        .filter-item label { display: block; margin-bottom: 5px; cursor: pointer; font-size: 14px; }
        .rating i { color: gold; }

        /* منطقة المنتجات */
        .product-area { flex-grow: 1; }
        .product-header { display: flex; justify-content: space-between; align-items: center; background: white; padding: 15px; margin-bottom: 20px; border-radius: 8px; box-shadow: 0 0 10px rgba(0, 0, 0, 0.05); }
        
        /* شبكة المنتجات */
        .products-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); gap: 25px; }
        .product-card { background-color: white; border: 1px solid #eee; border-radius: 8px; overflow: hidden; box-shadow: 0 2px 5px rgba(0, 0, 0, 0.05); transition: box-shadow 0.3s; }
        .product-card:hover { box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1); }
        .product-card img { width: 100%; height: 200px; object-fit: cover; }
        .product-info { padding: 15px; }
        .product-info h4 { font-size: 16px; margin-bottom: 5px; height: 40px; overflow: hidden; }
        .product-info .price { font-size: 20px; color: #ff471a; font-weight: 700; margin: 5px 0; }
        .seller-info { font-size: 12px; color: #999; margin-top: 5px; }
        .add-to-cart-btn { background-color: #ff471a; color: white; border: none; padding: 8px; border-radius: 4px; cursor: pointer; width: 100%; margin-top: 10px; }

        /* التجاوب */
        @media (max-width: 1024px) {
            .main-content { flex-direction: column; padding: 0 10px; }
            .sidebar { width: 100%; margin-left: 0; margin-bottom: 20px; }
            .search-bar { margin: 0 10px; max-width: none; }
            .navbar { flex-wrap: wrap; }
            .logo, .nav-links { margin-top: 10px; }
        }
        @media (max-width: 600px) {
            .navbar { padding: 10px; }
            .search-bar { order: -1; margin: 10px 0; }
            .nav-links { justify-content: space-around; display: flex; }
        }
    </style>
</head>
<body>

    <header class="navbar">
        <div class="logo">
            <a href="#">متجر النخبة</a>
        </div>
        <div class="search-bar">
            <input type="search" id="product-search" placeholder="البحث في كل شيء..." autocomplete="off">
            <button onclick="performSearch()"><i class="fas fa-search"></i></button>
            <div id="autocomplete-results" class="autocomplete-results" style="display: none;"></div>
        </div>
        <nav class="nav-links">
            <a href="#"><i class="fas fa-home"></i> الرئيسية</a>
            <a href="#"><i class="fas fa-user"></i> حسابي</a>
            <a href="#"><i class="fas fa-shopping-cart"></i> السلة (<span id="cart-count">0</span>)</a>
        </nav>
    </header>

    <div class="main-content">

        <aside class="sidebar">
            <h2><i class="fas fa-filter"></i> تصفية النتائج</h2>

            <div class="filter-group">
                <h3>الفئة</h3>
                <div class="filter-item"><label><input type="checkbox" data-filter="category" value="Electronics"> إلكترونيات</label></div>
                <div class="filter-item"><label><input type="checkbox" data-filter="category" value="Fashion"> أزياء</label></div>
                <div class="filter-item"><label><input type="checkbox" data-filter="category" value="Home"> مستلزمات منزلية</label></div>
            </div>

            <div class="filter-group">
                <h3>التقييم</h3>
                <div class="filter-item"><label><input type="radio" name="rating" data-filter="rating" value="4"> <span class="rating"><i class="fas fa-star"></i> 4 نجوم فأكثر</span></label></div>
                <div class="filter-item"><label><input type="radio" name="rating" data-filter="rating" value="3"> <span class="rating"><i class="fas fa-star"></i> 3 نجوم فأكثر</span></label></div>
            </div>

            <div class="filter-group">
                <h3>الشحن</h3>
                <div class="filter-item"><label><input type="checkbox" data-filter="shipping" value="Free"> شحن مجاني</label></div>
                <div class="filter-item"><label><input type="checkbox" data-filter="shipping" value="Fast"> توصيل سريع</label></div>
            </div>
            
            <div class="filter-group">
                <h3>البائع</h3>
                <div class="filter-item"><label><input type="checkbox" data-filter="seller_rating" value="5"> بائع موثوق (5/5)</label></div>
                <div class="filter-item"><label><input type="checkbox" data-filter="seller_rating" value="4"> تقييم بائع (4/5)</label></div>
            </div>

        </aside>

        <section class="product-area">
            <div class="product-header">
                <p>تم العثور على <span id="product-count">0</span> نتيجة.</p>
                <div>
                    <label for="sort-by">فرز حسب:</label>
                    <select id="sort-by" onchange="applyFilters()">
                        <option value="best_match">الأفضل مطابقة</option>
                        <option value="price_asc">السعر: الأقل للأعلى</option>
                        <option value="price_desc">السعر: الأعلى للأقل</option>
                        <option value="orders_desc">الأكثر طلباً</option>
                    </select>
                </div>
            </div>
            <div class="products-grid" id="products-container">
                </div>
        </section>

    </div>

    <script>
        // 1. قاعدة بيانات وهمية (Mock API) - تحتوي على خصائص التصفية
        const ALL_PRODUCTS = [
            { id: 101, name: "هاتف ذكي بشاشة 120Hz وكاميرا 108MP", price: 1850.50, category: "Electronics", rating: 4.8, orders: 1500, shipping: "Fast", seller_rating: 5, seller: "متجر التقنية", image: "https://via.placeholder.com/250x200/ff471a/FFFFFF?text=Smartphone" },
            { id: 102, name: "سماعات بلوتوث عازلة للضوضاء", price: 350.99, category: "Electronics", rating: 4.5, orders: 4500, shipping: "Free", seller_rating: 4, seller: "إلكترونيات سريعة", image: "https://via.placeholder.com/250x200/000000/FFFFFF?text=Headphones" },
            { id: 103, name: "فستان نسائي طويل أنيق", price: 150.00, category: "Fashion", rating: 4.2, orders: 800, shipping: "Free", seller_rating: 5, seller: "أزياء عصرية", image: "https://via.placeholder.com/250x200/c70039/FFFFFF?text=Dress" },
            { id: 104, name: "ماكينة قهوة إسبريسو احترافية", price: 920.00, category: "Home", rating: 4.9, orders: 300, shipping: "Fast", seller_rating: 5, seller: "مستلزمات منزلية", image: "https://via.placeholder.com/250x200/1a40ff/FFFFFF?text=Coffee+Maker" },
            { id: 105, name: "لابتوب مكتبي بمعالج قوي", price: 4200.00, category: "Electronics", rating: 4.1, orders: 120, shipping: "Fast", seller_rating: 3, seller: "تقنية المستقبل", image: "https://via.placeholder.com/250x200/505050/FFFFFF?text=Laptop" },
            { id: 106, name: "سجادة غرفة المعيشة الفاخرة", price: 450.00, category: "Home", rating: 3.9, orders: 600, shipping: "Free", seller_rating: 4, seller: "أثاث منزلي", image: "https://via.placeholder.com/250x200/7c00c7/FFFFFF?text=Rug" },
        ];

        let cartCount = 0;
        const productsContainer = document.getElementById('products-container');
        const searchInput = document.getElementById('product-search');
        const autocompleteResults = document.getElementById('autocomplete-results');

        // 2. دالة عرض التقييم بالنجوم
        function getRatingStars(rating) {
            const fullStars = Math.floor(rating);
            let starsHtml = '';
            for (let i = 0; i < fullStars; i++) {
                starsHtml += '<i class="fas fa-star"></i>';
            }
            for (let i = fullStars; i < 5; i++) {
                 starsHtml += '<i class="far fa-star"></i>';
            }
            return starsHtml;
        }

        // 3. دالة إنشاء بطاقة المنتج (تشبه مكون React)
        function createProductCard(product) {
            const card = document.createElement('div');
            card.className = 'product-card';
            const priceFormatted = product.price.toLocaleString('ar-SA', { style: 'currency', currency: 'SAR' });

            card.innerHTML = `
                <img src="${product.image}" alt="${product.name}" loading="lazy">
                <div class="product-info">
                    <h4>${product.name}</h4>
                    <div class="rating">${getRatingStars(product.rating)} (${product.rating})</div>
                    <p class="price">${priceFormatted}</p>
                    <p class="seller-info">الشحن: ${product.shipping} | البائع: ${product.seller}</p>
                    <button class="add-to-cart-btn" data-id="${product.id}" onclick="addToCart('${product.name}')">أضف إلى السلة</button>
                </div>
            `;
            return card;
        }

        // 4. دالة الإضافة إلى السلة
        function addToCart(productName) {
            cartCount++;
            document.getElementById('cart-count').textContent = cartCount;
            // في منصة حقيقية، هنا يتم إرسال طلب API للخلفية
            console.log(`تمت إضافة "${productName}". السلة الآن: ${cartCount}`);
        }

        // 5. دالة تطبيق التصفية والفرز (Mock Filter Logic)
        function applyFilters() {
            const activeFilters = {};
            // جمع خيارات التصفية النشطة
            document.querySelectorAll('.sidebar input:checked').forEach(input => {
                const filterType = input.dataset.filter;
                const filterValue = input.value;
                if (!activeFilters[filterType]) {
                    activeFilters[filterType] = [];
                }
                activeFilters[filterType].push(filterValue);
            });
            
            // الفرز
            const sortBy = document.getElementById('sort-by').value;
            let filteredProducts = [...ALL_PRODUCTS];

            // منطق التصفية
            filteredProducts = filteredProducts.filter(product => {
                let matches = true;

                for (const type in activeFilters) {
                    const values = activeFilters[type];
                    
                    if (type === 'rating' || type === 'seller_rating') {
                        // تصفية رقمية (Rating)
                        const requiredRating = parseFloat(values[0]);
                        if (product[type] < requiredRating) {
                             matches = false;
                        }
                    } else if (values.length > 0) {
                        // تصفية متعددة (Category, Shipping)
                         if (!values.includes(product[type])) {
                            matches = false;
                        }
                    }
                }
                
                // البحث بالكلمة المفتاحية
                const searchTerm = searchInput.value.toLowerCase();
                if (searchTerm && !product.name.toLowerCase().includes(searchTerm)) {
                    matches = false;
                }

                return matches;
            });

            // منطق الفرز
            if (sortBy === 'price_asc') {
                filteredProducts.sort((a, b) => a.price - b.price);
            } else if (sortBy === 'price_desc') {
                filteredProducts.sort((a, b) => b.price - a.price);
            } else if (sortBy === 'orders_desc') {
                filteredProducts.sort((a, b) => b.orders - a.orders);
            }
            
            // عرض المنتجات المصفاة
            renderProducts(filteredProducts);
        }

        // 6. دالة عرض المنتجات
        function renderProducts(productsList) {
            productsContainer.innerHTML = '';
            if (productsList.length === 0) {
                productsContainer.innerHTML = '<p style="grid-column: 1 / -1; text-align: center; padding: 50px;">عفواً، لا توجد منتجات مطابقة لخيارات التصفية/البحث.</p>';
            } else {
                productsList.forEach(product => {
                    productsContainer.appendChild(createProductCard(product));
                });
            }
            document.getElementById('product-count').textContent = productsList.length;
        }

        // 7. دالة محرك البحث الذكي (Autosuggest)
        function handleSearchInput() {
            const query = searchInput.value.toLowerCase();
            if (query.length < 2) {
                autocompleteResults.style.display = 'none';
                return;
            }

            // محاكاة استجابة Elasticsearch (البحث الجزئي + تصحيح الأخطاء)
            const suggestions = ALL_PRODUCTS
                .map(p => p.name)
                .filter(name => name.toLowerCase().includes(query))
                .slice(0, 5); // عرض أول 5 اقتراحات فقط

            autocompleteResults.innerHTML = '';
            if (suggestions.length > 0) {
                suggestions.forEach(suggestion => {
                    const div = document.createElement('div');
                    div.textContent = suggestion;
                    div.onclick = () => {
                        searchInput.value = suggestion;
                        autocompleteResults.style.display = 'none';
                        applyFilters(); // أداء البحث بعد اختيار الاقتراح
                    };
                    autocompleteResults.appendChild(div);
                });
                autocompleteResults.style.display = 'block';
            } else {
                autocompleteResults.style.display = 'none';
            }
        }
        
        // 8. دالة تنفيذ البحث
        function performSearch() {
            autocompleteResults.style.display = 'none';
            applyFilters();
        }

        // 9. ربط الأحداث وتشغيل التطبيق
        document.addEventListener('DOMContentLoaded', () => {
            // ربط الفلاتر بـ applyFilters
            document.querySelectorAll('.sidebar input').forEach(input => {
                input.addEventListener('change', applyFilters);
            });
            
            // ربط مربع البحث بـ handleSearchInput للاقتراحات
            searchInput.addEventListener('input', handleSearchInput);
            
            // إخفاء الاقتراحات عند النقر خارجها
            document.addEventListener('click', (e) => {
                if (!searchInput.contains(e.target) && !autocompleteResults.contains(e.target)) {
                    autocompleteResults.style.display = 'none';
                }
            });

            // تحميل المنتجات الأولية
            renderProducts(ALL_PRODUCTS);
        });
    </script>
</body>
</html>
