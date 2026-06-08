const express = require("express");
const cors = require("cors");
const axios = require("axios");

const app = express();

app.use(cors());
app.use(express.json());

app.get("/", (req, res) => {
  res.send("SevaSetu Backend Running 🚀");
});

app.post("/create-order", async (req, res) => {
  try {
    const orderId = "SEVASETU_" + Date.now();

    const response = await axios.post(
      "https://api.cashfree.com/pg/orders",
      {
        order_id: orderId,
        order_amount: 49,
        order_currency: "INR",

        customer_details: {
          customer_id: "USER_" + Date.now(),
          customer_name: "SevaSetu User",
          customer_email: "user@example.com",
          customer_phone: "9999999999",
        },

        order_meta: {
          return_url:
            "https://sevasetu-vle.web.app/payment-success?order_id={order_id}",
        },
      },
      {
        headers: {
          "x-client-id": process.env.CASHFREE_APP_ID,
          "x-client-secret": process.env.CASHFREE_SECRET_KEY,
          "x-api-version": "2023-08-01",
          "Content-Type": "application/json",
        },
      }
    );

    res.json(response.data);
  } catch (error) {
    console.error(
      error.response ? error.response.data : error.message
    );

    res.status(500).json({
      success: false,
      error: error.response
        ? error.response.data
        : error.message,
    });
  }
});

const PORT = process.env.PORT || 5000;

app.listen(PORT, () => {
  console.log(`Server Running On Port ${PORT}`);
});