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
