from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy
  
app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///consume_wise.db'
db = SQLAlchemy(app)

class Product(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    sustainability_score = db.Column(db.Float, nullable=False)
    health_impact = db.Column(db.String(200), nullable=False)
    environmental_impact = db.Column(db.String(200), nullable=False)

with app.app_context():
    db.create_all()

@app.route('/add_product', methods=['POST'])
def add_product():
    data = request.get_json()
    new_product = Product(
        name=data['name'],
        sustainability_score=data['sustainability_score'],
        health_impact=data['health_impact'],
        environmental_impact=data['environmental_impact']
    )
    db.session.add(new_product)
    db.session.commit()
    return jsonify({'message': 'Product added successfully'}), 201

@app.route('/products', methods=['GET'])
def get_products():
    products = Product.query.all()
    return jsonify([{
        'name': product.name,
        'sustainability_score': product.sustainability_score,
        'health_impact': product.health_impact,
        'environmental_impact': product.environmental_impact
    } for product in products]), 200

if __name__ == '__main__':
    app.run(debug=True)
. Create the Recommendation Engine
import pandas as pd
from sklearn.ensemble import RandomForestRegressor

# Placeholder data for training
data = pd.DataFrame({
    'feature1': [...],      # Example features
    'feature2': [...],
    'sustainability_score': [...],
})

# Train the model (this should be done with real data)
X = data[['feature1', 'feature2']]
y = data['sustainability_score']
model = RandomForestRegressor()
model.fit(X, y)

def predict_sustainability(features):
    return model.predict([features])
HTML (index.html)

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ConsumeWise</title>
</head>
<body>
    <h1>ConsumeWise: Product Information</h1>
    <div>
        <h2>Add a Product</h2>
        <form id="productForm">
            <input type="text" id="name" placeholder="Product Name" required>
            <input type="number" id="score" placeholder="Sustainability Score" required>
            <input type="text" id="health" placeholder="Health Impact" required>
            <input type="text" id="environmental" placeholder="Environmental Impact" required>
            <button type="submit">Add Product</button>
        </form>
    </div>
    <div>
        <h2>Products</h2>
        <ul id="productList"></ul>
    </div>

    <script>
        const form = document.getElementById('productForm');

        form.addEventListener('submit', async (e) => {
            e.preventDefault();
            const productName = document.getElementById('name').value;
            const sustainabilityScore = document.getElementById('score').value;
            const healthImpact = document.getElementById('health').value;
            const environmentalImpact = document.getElementById('environmental').value;

            const response = await fetch('/add_product', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({
                    name: productName,
                    sustainability_score: sustainabilityScore,
                    health_impact: healthImpact,
                    environmental_impact: environmentalImpact,
                }),
            });
            const data = await response.json();
            alert(data.message);
            loadProducts();
        });

        const loadProducts = async () => {
            const response = await fetch('/products');
            const products = await response.json();
            const productList = document.getElementById('productList');
            productList.innerHTML = '';
            products.forEach(product => {
                const li = document.createElement('li');
                li.textContent = ${product.name} - Sustainability Score: ${product.sustainability_score};
                productList.appendChild(li);
            });
        };
        
        loadProducts();
    </script>
</body>
</html>
