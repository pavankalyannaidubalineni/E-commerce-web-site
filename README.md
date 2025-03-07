Front-end Code (Client-side)
*`client/src/components/App.js`*
```
jsx
import React from 'react';
import { BrowserRouter, Route, Switch } from 'react-router-dom';
import Header from './Header';
import Footer from './Footer';
import ProductList from './ProductList';
import ProductDetail from './ProductDetail';

const App = () => {
  return (
    <BrowserRouter>
      <Header />
      <Switch>
        <Route path="/" exact component={ProductList} />
        <Route path="/products/:id" component={ProductDetail} />
      </Switch>
      <Footer />
    </BrowserRouter>
  );
};

export default App;
```

*`client/src/containers/AppContainer.js`*
```
jsx
import React, { useState, useEffect } from 'react';
import { connect } from 'react-redux';
import { fetchProducts } from '../actions/productActions';

const AppContainer = ({ products, fetchProducts }) => {
  useEffect(() => {
    fetchProducts();
  }, []);

  return (
    <div>
      {products.map((product) => (
        <ProductList key={product.id} product={product} />
      ))}
    </div>
  );
};

const mapStateToProps = (state) => {
  return {
    products: state.products,
  };
};

export default connect(mapStateToProps, { fetchProducts })(AppContainer);
```

Back-end Code (Server-side)
*`server/models/Product.js`*
```
const mongoose = require('mongoose');

const productSchema = new mongoose.Schema({
  name: String,
  description: String,
  price: Number,
  image: String,
});

const Product = mongoose.model('Product', productSchema);

module.exports = Product;
```

*`server/controllers/productController.js`*
```
const Product = require('../models/Product');

exports.getProducts = async (req, res) => {
  try {
    const products = await Product.find();
    res.json(products);
  } catch (error) {
    res.status(500).json({ message: 'Error fetching products' });
  }
};

exports.getProduct = async (req, res) => {
  try {
    const product = await Product.findById(req.params.id);
    res.json(product);
  } catch (error) {
    res.status(404).json({ message: 'Product not found' });
  }
};
```

*`server/routes/productRoutes.js`*
```
const express = require('express');
const router = express.Router();
const productController = require('../controllers/productController');

router.get('/', productController.getProducts);
router.get('/:id', productController.getProduct);

module.exports = router;
```

*`server/app.js`*
```
const express = require('express');
const app = express();
const productRoutes = require('./routes/productRoutes');

app.use(express.json());
app.use('/api/products', productRoutes);

const port = process.env.PORT || 3001;
app.listen(port, () => {
  console.log(`Server listening on port ${port}`);
});


