# Constant-movement
First game I have ever developed (using lockdown to learn). Creating an endless runner in which the only controls required are to jump at the correct time to move through the levels. Code is working well but just wanted to put it out there if I should add or adjust anything.

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Tilemaps;

public class PlayerControls : MonoBehaviour
{
    [SerializeField] private Rigidbody2D rb;
    [SerializeField] private Collider2D coll;
    [SerializeField] private Collider2D Enemy;
    [SerializeField] private LayerMask ground;
    [SerializeField] Animator anim;
    [SerializeField] private enum State {run, jump, falling, death}
    [SerializeField] private State state = State.run;
    [SerializeField] public GameObject gameOverText, restartButton;
    private void Start()
    {
        coll = GetComponent <Collider2D>();
        anim.GetComponent<Animator>();
        rb = GetComponent<Rigidbody2D>();
        gameOverText.SetActive(false);
        restartButton.SetActive(false);
    }

    private void Update()
    {
        if (Input.touchCount == 0 && coll.IsTouchingLayers(ground))
        {
            rb.velocity = new Vector2(10f, rb.velocity.y);
            state = State.run;
        }

        if (Input.touchCount == 1 && coll.IsTouchingLayers(ground))
        {
            Jump();
        }
                                                  
        VelocityState();
        anim.SetInteger("state", (int)state);
    }

    private void VelocityState()
    {
        if (state == State.jump)
        {
            if (rb.velocity.y < .1f)
            {
                state = State.falling;
            }

            if (state == State.falling)
            {
                if (coll.IsTouchingLayers(ground))
                {
                    state = State.run;
                }

            }

        }
    }


    void OnCollisionEnter2D(Collision2D col)
    {
        if (col.gameObject.tag == "Enemy")
        {
            gameOverText.SetActive(true);
            restartButton.SetActive(true);
            gameObject.SetActive(false);
         
        }
        
        if(col.gameObject.tag == "Eagle")
            if (state == State.falling)
                {
                    Destroy(col.gameObject);
                    Jump();
                }

        if (col.gameObject.tag == "Bee")
            if (state == State.falling)
            {
                Destroy(col.gameObject);
                Jump();
            }
    }

    private void Jump()
    {
        rb.velocity = new Vector2(10f, 50f);
        state = State.jump;
    }
}
