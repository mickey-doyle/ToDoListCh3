### Windows Form App To-Do List
- This uses dotnet 8.0 core
- Couldn't get VS2019 to work on my Windows VM so this was tested w/ VS2022 (with ReSharper)
- Snake case > camel case
- I included a standalone binary (ToDoList.exe in base directory) in case any issues come up running from source
 
### Form1.cs

```csharp
namespace ToDoApp
{
    public partial class main_form : Form
    {
        private TextBox task_input;
        private Button add_task_button;
        private Button complete_task_button;
        private ListBox task_list;

        public main_form()
        {
            // Initialize all controls in the constructor
            InitializeComponent();
        }

        private void InitializeComponent()
        {
            // Form setup
            this.Text = "To-Do";
            this.Size = new System.Drawing.Size(300, 400);

            // Task input textbox
            task_input = new TextBox
            {
                Location = new System.Drawing.Point(10, 10),
                Width = 180
            };
            this.Controls.Add(task_input);

            // Add task button
            add_task_button = new Button
            {
                Text = "Add",
                Location = new System.Drawing.Point(200, 10),
                Size = new System.Drawing.Size(90, 25)
            };
            add_task_button.Click += (sender, e) =>
            {
                if (!string.IsNullOrWhiteSpace(task_input.Text))
                {
                    task_list.Items.Add(task_input.Text);
                    task_input.Clear();
                }
            };
            this.Controls.Add(add_task_button);

            // Complete task button
            complete_task_button = new Button
            {
                Text = "Complete",
                Location = new System.Drawing.Point(200, 45),
                Size = new System.Drawing.Size(90, 25)
            };
            complete_task_button.Click += (sender, e) =>
            {
                if (task_list.SelectedIndex >= 0)
                {
                    var current_task = task_list.Items[task_list.SelectedIndex].ToString();
                    task_list.Items[task_list.SelectedIndex] = current_task.StartsWith("✓ ") ? current_task.Substring(2) : "✓ " + current_task;
                }
            };
            this.Controls.Add(complete_task_button);

            // Task listbox
            task_list = new ListBox
            {
                Location = new System.Drawing.Point(10, 80),
                Size = new System.Drawing.Size(280, 300),
                DrawMode = DrawMode.OwnerDrawFixed
            };
            task_list.DrawItem += task_list_draw_item;
            this.Controls.Add(task_list);
        }

        // Custom draw for task list
        private void task_list_draw_item(object sender, DrawItemEventArgs e)
        {
            e.DrawBackground();
            if (e.Index >= 0)
            {
                string task_text = task_list.Items[e.Index].ToString();
                bool is_task_completed = task_text.StartsWith("✓ ");
                Brush task_brush = is_task_completed ? Brushes.LightGray : SystemBrushes.WindowText;
                string display_task = is_task_completed ? "✓ " + task_text.Substring(2) : "  " + task_text;
                e.Graphics.DrawString(display_task, e.Font, task_brush, e.Bounds, StringFormat.GenericDefault);
            }
            e.DrawFocusRectangle();
        }
    }
}
