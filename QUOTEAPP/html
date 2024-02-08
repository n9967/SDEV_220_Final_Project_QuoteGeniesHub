from flask import Flask, render_template, redirect, url_for, flash
from flask_sqlalchemy import SQLAlchemy
from flask_wtf import FlaskForm
from wtforms import StringField, FloatField, IntegerField, validators

app = Flask(__name__)
app.config['SECRET_KEY'] = 'your_secret_key'
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///business_management.db'

db = SQLAlchemy(app)

# Models
class Product(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(255), unique=True, nullable=False)
    price = db.Column(db.Float, nullable=False)

class Quote(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    product_id = db.Column(db.Integer, db.ForeignKey('product.id'), nullable=False)
    quantity = db.Column(db.Integer, nullable=False)

# Forms
class ProductForm(FlaskForm):
    name = StringField('Product Name', [validators.DataRequired()])
    price = FloatField('Product Price', [validators.DataRequired(), validators.NumberRange(min=0)])

class QuoteForm(FlaskForm):
    product = StringField('Product Name', [validators.DataRequired()])
    quantity = IntegerField('Quantity', [validators.DataRequired(), validators.NumberRange(min=1)])

# Routes
@app.route('/')
def home():
    products = Product.query.all()
    return render_template('home.html', products=products)

@app.route('/add_product', methods=['GET', 'POST'])
def add_product():
    form = ProductForm()
    if form.validate_on_submit():
        new_product = Product(name=form.name.data, price=form.price.data)
        db.session.add(new_product)
        db.session.commit()
        flash(f'Product {form.name.data} added successfully!', 'success')
        return redirect(url_for('home'))
    return render_template('add_product.html', form=form)

@app.route('/generate_quote', methods=['GET', 'POST'])
def generate_quote():
    form = QuoteForm()
    products = Product.query.all()
    if form.validate_on_submit():
        product_name = form.product.data
        quantity = form.quantity.data
        product = Product.query.filter_by(name=product_name).first()
        if product:
            total_price = product.price * quantity
            return render_template('quote.html', product=product, quantity=quantity, total_price=total_price)
        else:
            flash(f'Product {product_name} not found!', 'danger')
    return render_template('generate_quote.html', form=form, products=products)

if __name__ == '__main__':
    db.create_all()
    app.run(debug=True)
