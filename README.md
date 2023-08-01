# ramjee

[Edit on StackBlitz ⚡️](https://stackblitz.com/edit/web-platform-yyhbqa)
<code>
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;

public class DeveloperProject
{
public int DeveloperId { get; set; }
public string FirstName { get; set; }
public string LastName { get; set; }
public int ProjectId { get; set; }
public string ProjectName { get; set; }
public int DaysWorked { get; set; }
}

public class Program
{
public static void Main()
{
string connectionString = "Data Source=your_server_name;Initial Catalog=your_database_name;User ID=your_username;Password=your_password;";
string query = @"
SELECT
d.developer_id,
d.first_name,
d.last_name,
p.project_id,
p.project_name,
SUM(DATEDIFF(DAY, dp.assignment_start_date, dp.assignment_end_date) + 1) AS days_worked
FROM
Developer_Project dp
JOIN
Developer d ON dp.developer_id = d.developer_id
JOIN
Project p ON dp.project_id = p.project_id
GROUP BY
d.developer_id, d.first_name, d.last_name, p.project_id, p.project_name
ORDER BY
d.developer_id, p.project_id;
";

        List<DeveloperProject> result = new List<DeveloperProject>();

        using (SqlConnection connection = new SqlConnection(connectionString))
        {
            connection.Open();
            using (SqlCommand command = new SqlCommand(query, connection))
            {
                using (SqlDataReader reader = command.ExecuteReader())
                {
                    while (reader.Read())
                    {
                        DeveloperProject developerProject = new DeveloperProject
                        {
                            DeveloperId = (int)reader["developer_id"],
                            FirstName = reader["first_name"].ToString(),
                            LastName = reader["last_name"].ToString(),
                            ProjectId = (int)reader["project_id"],
                            ProjectName = reader["project_name"].ToString(),
                            DaysWorked = (int)reader["days_worked"]
                        };
                        result.Add(developerProject);
                    }
                }
            }
        }

        // Now you have the result in the 'result' list, and you can use it as needed.
        foreach (var developerProject in result)
        {
            Console.WriteLine($"{developerProject.FirstName} {developerProject.LastName} worked on Project '{developerProject.ProjectName}' for {developerProject.DaysWorked} days.");
        }
    }

}
</code>
