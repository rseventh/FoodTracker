# FoodTracker
PROJECT 03 - FOOD TRACKER

using System;
using System.Data;
using System.Windows.Forms;

namespace FoodTracker
{
    public partial class Form1 : Form
    {
        DatabaseHelper db = new DatabaseHelper();

        public Form1()
        {
            InitializeComponent();

            btnAdd.Click += btnAdd_Click;
            btnDelete.Click += btnDelete_Click;
            btnClear.Click += btnClear_Click;

            LoadData();
        }

        private void LoadData() // makes sure information from the database is shown in the datagridview form.
        {
            try
            {
                DataTable dt = db.GetAllFoodItems(); // handles database operations
                dgvFoodItems.DataSource = dt; //stores that information in a table

              
                dgvFoodItems.AutoSizeColumnsMode = DataGridViewAutoSizeColumnsMode.Fill; //helps wiht the colm readability
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error loading data: " + ex.Message);
            }
        }

        private void btnAdd_Click(object sender, EventArgs e) // add button. uses ifs, try catch. if you don't enter something or the incorrect text, a message box will pop up.
        {
            try
            {
                string foodName = txtFoodName.Text.Trim();
                string ratingText = txtRating.Text.Trim();
                string notes = txtNotes.Text.Trim();

                if (string.IsNullOrEmpty(foodName))
                {
                    MessageBox.Show("Please enter a food name!");
                    return;
                }

                if (!int.TryParse(ratingText, out int rating))
                {
                    MessageBox.Show("Please enter a valid rating (1-5)!");
                    return;
                }

                if (rating < 1 || rating > 5)
                {
                    MessageBox.Show("Rating must be between 1 and 5!");
                    return;
                }

                db.AddFoodItem(foodName, rating, notes);
                MessageBox.Show("Food item added successfully!");

                txtFoodName.Clear();
                txtRating.Clear();
                txtNotes.Clear();

                LoadData();
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error: " + ex.Message);
            }
        }

        private void btnDelete_Click(object sender, EventArgs e) //delete button, used to delete the row you want deleted. If you don't select one, you will be prompted to do so otherwise.
        {
            try
            {
                if (dgvFoodItems.SelectedRows.Count > 0)
                {
                    int id = Convert.ToInt32(dgvFoodItems.SelectedRows[0].Cells["Id"].Value);

                    DialogResult result = MessageBox.Show(
                        "Are you sure you want to delete this item?",
                        "Confirm Delete",
                        MessageBoxButtons.YesNo);

                    if (result == DialogResult.Yes)
                    {
                        db.DeleteFoodItem(id);
                        MessageBox.Show("Item deleted successfully!");
                        LoadData();
                    }
                }
                else
                {
                    MessageBox.Show("Please select a row to delete!");
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error: " + ex.Message);
            }
        }

        private void btnClear_Click(object sender, EventArgs e)
        {
            txtFoodName.Clear();
            txtRating.Clear();
            txtNotes.Clear();
        }
    }
}
