JanitriLoginAutomation
package base;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.annotations.AfterMethod;
import org.testng.annotations.BeforeMethod;

public class BaseTest {
    protected WebDriver driver;

    @BeforeMethod
    public void setUp() {
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.get("https://dev-dash.janitri.in/");
    }

    @AfterMethod
    public void tearDown() {
        if (driver != null) {
            driver.quit();
        }
    }
}
package pages;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class LoginPage {
    private WebDriver driver;

    // Locators
    private By userIdInput = By.id("email");   // Adjust if locator differs
    private By passwordInput = By.id("password");
    private By loginButton = By.xpath("//button[contains(text(),'Login')]");
    private By eyeIcon = By.xpath("//button[@aria-label='toggle password visibility']");
    private By errorMsg = By.xpath("//p[@class='error']");

    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }

    // Actions
    public void enterUserId(String userId) {
        driver.findElement(userIdInput).sendKeys(userId);
    }

    public void enterPassword(String password) {
        driver.findElement(passwordInput).sendKeys(password);
    }

    public void clickLogin() {
        driver.findElement(loginButton).click();
    }

    public boolean isLoginButtonEnabled() {
        return driver.findElement(loginButton).isEnabled();
    }

    public void togglePasswordVisibility() {
        driver.findElement(eyeIcon).click();
    }

    public String getErrorMessage() {
        return driver.findElement(errorMsg).getText();
    }
}
package tests;

import base.BaseTest;
import pages.LoginPage;
import org.testng.Assert;
import org.testng.annotations.Test;

public class LoginTest extends BaseTest {

    @Test
    public void testLoginButtonDisabledWhenFieldsEmpty() {
        LoginPage loginPage = new LoginPage(driver);
        Assert.assertFalse(loginPage.isLoginButtonEnabled(), "Login button should be disabled when fields are empty");
    }

    @Test
    public void testInvalidLoginShowsErrorMsg() {
        LoginPage loginPage = new LoginPage(driver);
        loginPage.enterUserId("wrong@janitri.in");
        loginPage.enterPassword("wrongpass");
        loginPage.clickLogin();
        Assert.assertTrue(loginPage.getErrorMessage().contains("Invalid"), "Error message should appear");
    }

    @Test
    public void testPasswordMaskedAndUnmasked() {
        LoginPage loginPage = new LoginPage(driver);
        loginPage.enterPassword("Password123");
        loginPage.togglePasswordVisibility();
        // Validation depends on how the app exposes visible text
        // Example: check attribute type = "text"
        String type = driver.findElement(By.id("password")).getAttribute("type");
        Assert.assertEquals(type, "text", "Password should be visible after clicking eye icon");
    }

    @Test
    public void testUIElementsPresence() {
        LoginPage loginPage = new LoginPage(driver);
        Assert.assertTrue(driver.getTitle().contains("Janitri"), "Page title should contain 'Janitri'");
    }
}
