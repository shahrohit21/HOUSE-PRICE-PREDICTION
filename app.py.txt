import streamlit as st
import pickle
import numpy as np

# Page setup
st.set_page_config(page_title="AI House Price Predictor", page_icon="🏠", layout="wide")

# Load model
model = pickle.load(open("final_model.pkl", "rb"))

# Sidebar
st.sidebar.title("🏠 Settings")
st.sidebar.markdown("### Choose Model")
model_choice = st.sidebar.selectbox("Model", ["Gradient Boosting", "Random Forest", "XGBoost"])

st.sidebar.markdown("### About")
st.sidebar.info("This AI model predicts house prices based on property features.")

# Custom CSS
st.markdown("""
<style>
.big-title {
    font-size: 40px;
    font-weight: bold;
    color: #2a5298;
    text-align: center;
}
.sub-text {
    text-align: center;
    color: grey;
}
.card {
    background-color: #f0f2f6;
    padding: 20px;
    border-radius: 15px;
}
</style>
""", unsafe_allow_html=True)

# Title
st.markdown('<p class="big-title">🏠 AI House Price Prediction</p>', unsafe_allow_html=True)
st.markdown('<p class="sub-text">Enter details and get instant smart prediction 💡</p>', unsafe_allow_html=True)

# Layout
col1, col2 = st.columns([2, 1])

with col1:
    st.markdown("### 📊 Property Details")

    area = st.slider("Area (sq ft)", 500, 5000, 1500)
    bedrooms = st.selectbox("Bedrooms", [1, 2, 3, 4, 5])
    bathrooms = st.selectbox("Bathrooms", [1, 2, 3, 4])
    floors = st.selectbox("Floors", [1, 2, 3])

    location = st.selectbox("📍 Location", ["Ahmedabad West", "Ahmedabad East", "Gandhinagar", "Other"])

    parking = st.radio("🚗 Parking Available", ["Yes", "No"])
    furnishing = st.radio("🛋 Furnishing", ["Furnished", "Semi-Furnished", "Unfurnished"])

# Convert categorical inputs
parking_val = 1 if parking == "Yes" else 0
furnishing_val = {"Unfurnished": 0, "Semi-Furnished": 1, "Furnished": 2}[furnishing]

# Dummy encoding for location (example)
loc_map = {
    "Ahmedabad West": 2,
    "Ahmedabad East": 1,
    "Gandhinagar": 3,
    "Other": 0
}
location_val = loc_map[location]

# Right panel
with col2:
    st.markdown("### 📈 Prediction Panel")

    if st.button("🚀 Predict Price"):
        data = np.array([[area, bedrooms, bathrooms, floors, parking_val, furnishing_val, location_val]])

        prediction = model.predict(data)

        st.success(f"💰 Estimated Price: ₹ {prediction[0]:,.2f}")

        st.progress(100)
        st.balloons()

    st.markdown("---")
    st.markdown("### 💡 Tips")
    st.info("✔ Larger area increases price\n✔ Better location = higher value\n✔ Furnished homes cost more")

# Footer
st.markdown("---")
st.markdown("Made with ❤️ by Rohit | AI/ML Project 🚀")