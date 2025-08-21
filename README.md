<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Vape Shop</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #111;
      color: #fff;
      margin: 0;
      padding: 0;
    }
    header {
      background: #222;
      padding: 20px;
      text-align: center;
      font-size: 28px;
      font-weight: bold;
      color: #00e0ff;
    }
    nav {
      display: flex;
      justify-content: center;
      background: #333;
      flex-wrap: wrap;
    }
    nav button {
      background: #444;
      border: none;
      padding: 14px 24px;
      margin: 5px;
      font-size: 16px;
      color: #fff;
      cursor: pointer;
      border-radius: 8px;
      transition: 0.3s;
    }
    nav button:hover {
      background: #00e0ff;
      color: #111;
    }
    #searchBar {
      display: block;
      margin: 15px auto;
      padding: 12px;
      width: 90%;
      max-width: 400px;
      font-size: 16px;
      border: none;
      border-radius: 8px;
      outline: none;
      text-align: center;
    }
    .products {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
      gap: 20px;
      padding: 20px;
    }
    .product {
      background: #222;
      border-radius: 10px;
      padding: 10px;
      text-align: center;
      box-shadow: 0 0 10px rgba(0,0,0,0.6);
    }
    .product img {
      width: 100%;
      border-radius: 10px;
      max-height: 200px;
      object-fit: cover;
    }
    .product p {
      margin-top: 10px;
      font-size: 16px;
      color: #ddd;
    }
    .add-section {
      text-align: center;
      padding: 15px;
      background: #222;
    }
    .add-section input, .add-section select {
      margin: 5px;
      padding: 8px;
      border-radius: 5px;
      border: none;
    }
    .add-section button {
      background: #00e0ff;
      border: none;
      padding: 10px 20px;
      margin: 5px;
      font-size: 14px;
      color: #111;
      cursor: pointer;
      border-radius: 8px;
      transition: 0.3s;
    }
    .add-section button:hover {
      background: #00b5cc;
    }
  </style>
</head>
<body>

  <header>Vape Shop - Disposables & Vape Juice</header>

  <!-- 🔍 Search bar -->
  <input type="text" id="searchBar" placeholder="Search flavor...">

  <!-- Navigation buttons load here -->
  <nav id="navBar"></nav>

  <!-- 🆕 Add new flavor section -->
  <div class="add-section">
    <select id="category"></select>
    <input type="text" id="flavorName" placeholder="Flavor Name">
    <input type="file" id="flavorImage" accept="image/*">
    <button onclick="addProduct()">Add Flavor</button>
  </div>

  <!-- 🆕 Add new brand section -->
  <div class="add-section">
    <input type="text" id="newBrand" placeholder="New Brand Name">
    <button onclick="addBrand()">Add New Brand</button>
  </div>

  <section id="products" class="products"></section>

  <script>
    // 🔹 Default starter products
    const defaultProducts = {
      airbar: [],
      geekbar: [],
      breeze: [],
      juice: []
    };

    // Load from LocalStorage or use default
    let products = JSON.parse(localStorage.getItem("vapeProducts")) || defaultProducts;

    // 🔹 Render navigation buttons
    function renderNav() {
      const nav = document.getElementById("navBar");
      nav.innerHTML = "";
      const categories = Object.keys(products);

      categories.forEach(cat => {
        const btn = document.createElement("button");
        btn.textContent = cat.charAt(0).toUpperCase() + cat.slice(1);
        btn.onclick = () => showProducts(cat);
        nav.appendChild(btn);
      });

      // Always add "Show All" button at the end
      const showAllBtn = document.createElement("button");
      showAllBtn.textContent = "Show All";
      showAllBtn.onclick = () => showProducts("all");
      nav.appendChild(showAllBtn);

      // Update dropdown
      const select = document.getElementById("category");
      select.innerHTML = "";
      categories.forEach(cat => {
        const opt = document.createElement("option");
        opt.value = cat;
        opt.textContent = cat.charAt(0).toUpperCase() + cat.slice(1);
        select.appendChild(opt);
      });
    }

    // 🔹 Show products by category
    function showProducts(type) {
      const container = document.getElementById('products');
      container.innerHTML = "";

      let list = [];
      if (type === "all") {
        Object.values(products).forEach(arr => list = list.concat(arr));
      } else {
        list = products[type] || [];
      }

      list.forEach(p => {
        container.innerHTML += `
          <div class="product">
            <img src="${p.img}" alt="${p.name}">
            <p>${p.name}</p>
          </div>
        `;
      });
    }

    // 🔹 Search filter
    document.getElementById("searchBar").addEventListener("input", function() {
      const query = this.value.toLowerCase();
      const container = document.getElementById('products');
      container.innerHTML = "";

      let allProducts = [];
      Object.values(products).forEach(arr => allProducts = allProducts.concat(arr));

      const filtered = allProducts.filter(p => p.name.toLowerCase().includes(query));

      filtered.forEach(p => {
        container.innerHTML += `
          <div class="product">
            <img src="${p.img}" alt="${p.name}">
            <p>${p.name}</p>
          </div>
        `;
      });
    });

    // 🔹 Add new product
    function addProduct() {
      const category = document.getElementById("category").value;
      const name = document.getElementById("flavorName").value;
      const fileInput = document.getElementById("flavorImage");

      if (!name || fileInput.files.length === 0) {
        alert("Please enter a flavor name and select an image.");
        return;
      }

      const file = fileInput.files[0];
      const reader = new FileReader();
      reader.onload = function(e) {
        const newProduct = { name: name, img: e.target.result };
        products[category].push(newProduct);

        // Save permanently
        localStorage.setItem("vapeProducts", JSON.stringify(products));

        showProducts(category);
      };
      reader.readAsDataURL(file);

      document.getElementById("flavorName").value = "";
      fileInput.value = "";
    }

    // 🔹 Add new brand
    function addBrand() {
      const brandInput = document.getElementById("newBrand");
      const brandName = brandInput.value.trim().toLowerCase();

      if (!brandName) {
        alert("Please enter a brand name.");
        return;
      }

      if (products[brandName]) {
        alert("This brand already exists.");
        return;
      }

      // Add new brand
      products[brandName] = [];

      // Save to LocalStorage
      localStorage.setItem("vapeProducts", JSON.stringify(products));

      // Re-render nav and dropdown
      renderNav();

      // Clear input
      brandInput.value = "";
    }

    // First render
    renderNav();
    showProducts("airbar");
  </script>

</body>
</html>
 
