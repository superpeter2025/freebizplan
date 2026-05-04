import streamlit as st
import requests
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer
from reportlab.lib.styles import getSampleStyleSheet
from io import BytesIO

# --------------------------
# CONFIG
# --------------------------
API_KEY = st.secrets["DEEPSEEK_API_KEY"]
API_URL = "https://api.deepseek.com/v1/chat/completions"
MODEL = "deepseek-chat"

st.set_page_config(page_title="AI Business Plan Generator")
st.title("AI Business Plan Generator")
st.write("Answer 10 questions and get a full business plan instantly.")

# --------------------------
# FORM
# --------------------------
with st.form("business_form"):
    name = st.text_input("1. Your name")
    email = st.text_input("2. Email")
    business_name = st.text_input("3. Business name")
    idea = st.text_area("4. Business idea")
    target_customer = st.text_area("5. Target customers")
    problem = st.text_area("6. Problem you're solving")
    revenue = st.text_area("7. Revenue model")
    competition = st.text_area("8. Competitors")
    marketing = st.text_area("9. Marketing strategy")
    budget = st.text_input("10. Startup budget")

    submitted = st.form_submit_button("Generate Business Plan")

# --------------------------
# PROMPT
# --------------------------
def build_prompt(data):
    return f"""
You are an expert business consultant.

Create a clear, structured business plan with:

1. Executive Summary
2. Problem & Opportunity
3. Target Market
4. Revenue Model
5. Marketing Strategy
6. Competition Analysis
7. Startup Budget
8. Action Plan (5 steps)

Be concise, practical, and actionable.

DATA:
Name: {data['name']}
Business: {data['business_name']}
Idea: {data['idea']}
Target: {data['target_customer']}
Problem: {data['problem']}
Revenue: {data['revenue']}
Competition: {data['competition']}
Marketing: {data['marketing']}
Budget: {data['budget']}
"""

# --------------------------
# DEEPSEEK CALL
# --------------------------
def generate_plan(prompt):
    headers = {
        "Authorization": f"Bearer {API_KEY}",
        "Content-Type": "application/json"
    }

    payload = {
        "model": MODEL,
        "messages": [
            {"role": "user", "content": prompt}
        ]
    }

    response = requests.post(API_URL, headers=headers, json=payload)
    response.raise_for_status()

    return response.json()["choices"][0]["message"]["content"]

# --------------------------
# PDF GENERATION
# --------------------------
def create_pdf(text):
    buffer = BytesIO()
    doc = SimpleDocTemplate(buffer)
    styles = getSampleStyleSheet()

    story = []
    for line in text.split("\n"):
        story.append(Paragraph(line, styles["Normal"]))
        story.append(Spacer(1, 6))

    doc.build(story)
    buffer.seek(0)
    return buffer

# --------------------------
# RUN APP
# --------------------------
if submitted:
    data = {
        "name": name,
        "email": email,
        "business_name": business_name,
        "idea": idea,
        "target_customer": target_customer,
        "problem": problem,
        "revenue": revenue,
        "competition": competition,
        "marketing": marketing,
        "budget": budget
    }

    prompt = build_prompt(data)

    with st.spinner("Generating your business plan..."):
        try:
            plan = generate_plan(prompt)

            st.success("Business plan generated!")

            st.text_area("Your Business Plan", plan, height=500)

            # TXT download
            st.download_button(
                "Download TXT",
                plan,
                file_name="business_plan.txt"
            )

            # PDF download
            pdf = create_pdf(plan)
            st.download_button(
                "Download PDF",
                pdf,
                file_name="business_plan.pdf"
            )

        except Exception as e:
            st.error(f"Error: {e}")
